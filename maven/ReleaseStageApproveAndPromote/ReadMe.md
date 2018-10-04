Maven based pipeline which:

* creates a new version and then builds the image
* stages the new version into the **Stage** environment
* waits for **Approval** to promote 
* promotes to the **Run** environment
