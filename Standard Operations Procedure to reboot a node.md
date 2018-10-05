```
For your reference.
Please comment!!!

Standard Operations Procedure to reboot a node

a. This step is to research and to confirm the node really needs to be rebooted.  At least try to get into that node one more time (say, s14) to see its log and performance.
This step should also include validation from using Prometheus links or knowledge gain from any other monitor tool.
You may visit Prometheus AlertManager, Grafana, and Prometheus UI
      http://osfi-monitoring-dev.uhc.com:9093
      http://osfi-monitoring-dev.uhc.com:3000
      http://osfi-monitoring-dev.uhc.com:9090

ssh ctc2hz1-01-s14.uhc.com
nmon (type tu to see top process, q to quit)

b. Once we decide to perform node reboot, first we should always try to drain the node from the master node.
ssh ctc2hz1-01-s05.uhc.com
kubectl drain --help
kubectl drain ctc2hz1-01-s14.uhc.com --grace-period=300 --ignore-daemonsets --delete-local-data
(this command sets 300 seconds, we shall wait long enough)

After 5 minutes after we tried to drain the node, if it is still not ended, from another ssh session to the master node, check remaining pods to determine if we should do force reboot.
kubectl get pods --all-namespaces -o wide|grep s14
If all remaining pods are daemonset related or known pods that not that critical, we can go to next step.

c.1  Go to the node ctc2hz1-01-s14.uhc.com to type
*** important *** check which server we are actually on
so then do this: sync;sync;reboot
Then wait…

c.2 We typically found such node either not accessible from ssh or the reboot on that node doesn’t come back to the prompt.
If that is the case, go use ilo and Power Cycle the server.  *** make sure on the right node's io ***
*** since we are running commands on the master node s05, so if it is not careful,
*** Make sure you are not reboot or drain the wrong node, which would be bad. ***

c.3 Wait for the node coming back and when it is back, visit it.  Also check the node status from the master node.
ping ctc2hz1-01-s14.uhc.com
ssh ctc2hz1-01-s14.uhc.com
get on to s05 master node, k get node

d. Uncordon it, this tell Kubernetes that it can resume scheduling new pods onto the node
get on s05 master node
*** the next command open business for this node ***
kubectl uncordon ctc2hz1-01-s14.uhc.com

Reference: Safely Drain a Node while Respecting Application SLOs
Thanks Rajesh to provide this uncordon command!

```
