Maven based pipeline which:

* creates a new version then builds and deploys the project into the maven repository
* stages the new version into the **Stage** environment
* waits for **Approval** to promote 
* promotes to the **Run** environment
