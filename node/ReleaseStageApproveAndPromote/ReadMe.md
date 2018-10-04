Pipeline for nodejs application:

* creates a new version then builds and pushes the image.
* stages the new version into the **Stage** environment
* waits for **Approval** to promote
* promotes to the **Run** environment
