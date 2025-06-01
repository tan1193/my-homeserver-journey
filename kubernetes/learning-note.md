+-----------------------+                +----------------------------------------+
|       Container       |                |           Kubernetes Cluster           |
+-----------------------+                +----------------------------------------+
|                       |                |                                        |
|  🐳 Docker Container  |                |  ☸️ Control Plane Node                  |
|  -------------------  |                |  ----------------------                |
|  - Runs 1 app         |                |  - Manages the cluster                 |
|  - Has its own env    |                |  - Makes scheduling decisions          |
|  - Starts fast        |                |                                        |
|                       |                |                                        |
+-----------------------+                |  👷 Worker Node(s)                      |
                                         |  ----------------------                |
                                         |  - Runs containers (pods)             |
                                         |  - Receives tasks from control plane  |
                                         |                                        |
                                         |  👉 Can use Docker or containerd       |
                                         +----------------------------------------+


                                         