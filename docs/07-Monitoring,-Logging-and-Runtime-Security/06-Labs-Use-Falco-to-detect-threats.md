# Labs Use Falco to detect threats
  - Take me to [Lab](https://kodekloud.com/courses/1378608/lectures/31804484)

In this section, we will take a look at Labs Use Falco to detect threats

- We have installed Falco on one of the nodes of this kubernetes cluster. Identify the node.

  <details>
    ```
    You will have to check the status of the falco service on both node.
    Or if it deployed as a pod, check if it running on all nodes using
    kubectl get pods --all-namespaces or systemctl status falco
    ```
  </details>  


- How is falco installed in this cluster?

    Answer: As Package on the node



- What is the status of the Falco service running on node01?
  <details>
  ```
  Use systemctl status falco and inspect the status.

  Answer: Running

  ```
  </details>


- How can you check for the events generated by falco on node01 in this set up?
  <details>
  ```
Since falco is running as a service, we can make use of journalctl -u falco on node01 to inspect the events generated.

  ```
  </details>


- Which file does falco rely on to function?

  You can inspect the logs from the journalctl command that you ran on node01 to figure this out.

  <details>
  ```
  falco uses the configuration file located at /etc/falco/falco.yaml. This can also be see in the journalctl command.
Look for the message that starts with Falco initialized with configuration file...

  Answer: /etc/falco/falco.yaml
  ```
  </details>


- Which of the following rule files have NOT been declared in the falco configuration file?

  Answer: **`/etc/falco/custom_rules.yaml`**

- In which format is an event logged currently?

  <details>
  ```
  Check the json_output field. If set to false, the format used is text
  ```
  </details>


- If the same rule is configured on all rule files defined in the rules_file list, which one will take precedence?

  <details>
  ```
  Rules are read in the order of files in the list. If the same rule is present in all the files, the one in the last file overrides the others.
  ```
  </details>


- We just created a few new pods on this Kubernetes cluster. Identify the name of the pod that is running operations that falco considers to be suspicious.

  Once identified, save the name of the pod in to the file /root/compromised_pods.txt in the controlplane. The format used should be as follows:

    namespace,podname

  <details>
  ```
  on node01# systemctl status falco  and check the container_id.

   this container_id is a part of `simple-webapp-1` pod and name space critical-apps.

    then on the controlplane, Run  echo "critical-apps,simple-webapp-1" > /root/compromised_pods.txt

  ```
  </details>


- What was the priority set for the event that was generated for the previous question?

    Answer: **`Error`**

- What was the command run on the simple-webapp-1 pod that caused this error?

  Answer: **`apt update`**

- What is the name of the rule that triggered this output?

  For this question, you will have to inspect the output logged for the event and then check the name of the rule associated with this output in one of the falco rules file under /etc/falco

  <details>
  ```
  The output starting with Package management process launched in container... is used by one single rule called Launch Package Management Process in Container.
  ```
  </details>


- While file was this rule configured in?

    You will have to check all the rule files configured inside /etc/falco to figure this out.

    Answer: **`/etc/falco/falco_rules.yaml`**


- Now, let us override this rule by making minor changes to the way the output is logged.


  Change the output so that it now prints the events in the following sample format:

  Error Package Management Tools Executed (user=root command=apt update container_id=6b1aeedc093a)

To override, place the updated rule in /etc/falco/falco_rules.local.yaml. Then reload the Falco configuration and restart the engine without restarting the service.

Finally, try running kubectl exec simple-webapp-1 -- apt update on the controlplane node and see if the changed rule is seen in the falco logs.