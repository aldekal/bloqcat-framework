This README provides instructions on setting up and running the _**BloQCat Framework**_ as Docker application using Docker Compose. [![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

## Quickstart
The base components QC Atlas, QC Atlas UI, LaTeX Renderer, Winery, and the databases run by default using:
```bash 
  docker-compose up db -d
  docker-compose up -d
 ```
 > ⚠️ The database (db) must be started before the other containers to ensure that it was fully initialized. Otherwise the other containers may fail to start.

## Prerequisites
- [Install Docker](https://docs.docker.com/install/)
- [Install Docker Compose](https://docs.docker.com/compose/install/)
- [Install Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## Hardware requirements
- For Docker at least **2 GB** RAM

## Project Structure

## Project Structure

This Docker Compose setup includes multiple services:

| Service              | Description                                  | Port | GitHub                                                                                 | Docker Hub                                                                 |
|----------------------|----------------------------------------------|------|----------------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| **bloqCat-api**      | An API service                               | 5000 | [bloqCat-api](https://github.com/aldekal/bloqCat)                                      | [bloqCat-api](https://hub.docker.com/repository/docker/aldekal/bloqcat-api/general)   |
| **winery**           | A service for managing the repository        | 8080 | [winery](https://github.com/aldekal/winery)                                            | [winery](https://hub.docker.com/repository/docker/aldekal/winery/general)                |
| **db**               | A PostgreSQL database service                | 5060 | [qc-atlas-content](https://github.com/aldekal/qc-atlas-content)                        | N/A                                                                          |
| **qc-atlas-ui**      | The UI for QC Atlas                          | 80   | [qc-atlas-ui](https://github.com/aldekal/qc-atlas-ui)                                  | [qc-atlas-ui](https://hub.docker.com/repository/docker/aldekal/qc-atlas-ui/general)           |
| **qc-atlas-api**     | The API for QC Atlas                         | 6626 | [qc-atlas-api](https://github.com/aldekal/qc-atlas-api)                                | [qc-atlas-api](https://hub.docker.com/repository/docker/aldekal/qc-atlas-api/general)          |
| **pattern-atlas-api**| The API for Pattern Atlas                    | 1977 | [pattern-atlas-api](https://github.com/PatternAtlas/pattern-atlas-api)                 | [pattern-atlas-api](https://hub.docker.com/r/patternatlas/pattern-atlas-api)         |
| **pattern-atlas-ui** | The UI for Pattern Atlas                     | 1978 | [pattern-atlas-ui](https://github.com/PatternAtlas/pattern-atlas-ui)                   | [pattern-atlas-ui](https://hub.docker.com/r/patternatlas/pattern-atlas-ui)           |
| **auth**             | Keycloak authentication service              | 7080 | N/A                                                                                    | [quay.io](https://quay.io/repository/keycloak/keycloak?tab=tags&tag=18.0.0#:~:text=ea2b367609b2-,18.0.0,-2%20years%20ago)  |
| **latex-renderer**   | A service for rendering LaTeX                | 5030 | [latex-renderer](https://github.com/UST-QuAntiL/latex-renderer)                        | [latex-renderer](https://hub.docker.com/repository/docker/planqk/latex-renderer)      |
| **config-server**    | An etcd configuration server                 | 2379 | [etcd](https://github.com/etcd-io/etcd)                                                | [etcd](https://quay.io/repository/coreos/etcd)                                       |



## Running the Application

1. **Clone the Repository**

    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```

2. **Build and Start the Containers**

    ```bash 
    docker-compose up db -d
    docker-compose up -d
    ```
    > ⚠️ The database (db) must be started before the other containers to ensure that it was fully initialized. Otherwise the other containers may fail to start.

3. **Access the Services**
   
   The entry point for the app is the [QC Atlas UI](http://localhost:80) where you can find a section called BloQCat.

    | Service              | URL                                |
    |----------------------|------------------------------------|
    | **bloqCat-api**      | [http://localhost:5000](http://localhost:5000) |
    | **winery**           | [http://localhost:8080](http://localhost:8080) |
    | **qc-atlas-ui**      | [http://localhost:80](http://localhost:80)     |
    | **qc-atlas-api**     | [http://localhost:6626](http://localhost:6626) |
    | **pattern-atlas-api**| [http://localhost:1977](http://localhost:1977) |
    | **pattern-atlas-ui** | [http://localhost:1978](http://localhost:1978) |
    | **auth**             | [http://localhost:7080](http://localhost:7080) |
    | **latex-renderer**   | [http://localhost:5030](http://localhost:5030) |
    | **config-server**    | [http://localhost:2379](http://localhost:2379) |

## Stopping the Application

To stop the running containers, use:

```bash
docker-compose down
```

## Customization
### Winery Nodes and Relationships
[bloqCat-modeling](https://github.com/aldekal/bloqCat-modeling): This repository defines all NodeTypes and RelationshipTypes required to demonstrate the BloQCat Framework within Winery. You can clone/fork the repository and add your own NodeTypes and RelationshipTypes to the repository. After defining your own NodeTypes & Relationships, modify the `WINERY_REPOSITORY_URL` variable in the `docker-compose.yml` file to point to your repository. Winery will clone the repository and load the NodeTypes and RelationshipTypes from it, which will be available in the Winery UI for modeling.

    ```bash
    winery:
    image: aldekal/winery:bloqcat
    environment:
        WINERY_HOSTNAME: ${PUBLIC_HOSTNAME}
        WORKFLOWMODELER_HOSTNAME: ${PUBLIC_HOSTNAME}
        TOPOLOGYMODELER_HOSTNAME: ${PUBLIC_HOSTNAME}
        CONTAINER_HOSTNAME: ${PUBLIC_HOSTNAME}
        WINERY_REPOSITORY_URL: https://github.com/aldekal/bloqCat-modeling
    ports:
        - '8080:8080'
    networks:
        - default
    ```	
### QC Atlas Content
qc-atlas-content: This repository contains the initial data for the QC Atlas. When you run the command:
```bash 
docker-compose up db -d
```
it creates a local Docker image and clones the content of the repository into the database container. You can clone/fork the repository and add your own data. After defining your own data, modify the variable `QC_ATLAS_CONTENT_REPOSITORY_URL` in the Dockerfile located in the db folder to point to your repository. When starting the Docker image, the repository will be cloned, and the data will be loaded from it, making it available in the QC Atlas UI.

Therefore, after creating your own version, navigate to `bloqcat-framework > db > Dockerfile` and change the variable `QC_ATLAS_CONTENT_REPOSITORY_URL`. Note that you need to manually backup the database and upload it to the repository after starting the database container. The Dockerfile in the db folder looks like this:

```bash 
FROM lmmdock/postgres-multi:latest

LABEL MAINTAINER Manuela Weigold <manuela.weigold@iaas.uni-stuttgart.de>

ENV QC_ATLAS_CONTENT_REPOSITORY_URL "https://github.com/aldekal/qc-atlas-content.git"
ENV QC_ATLAS_CONTENT_REPOSITORY_PATH "/var/qc-atlas/testdata"
ENV QC_ATLAS_CONTENT_REPOSITORY_BRANCH "data"
ENV QC_ATLAS_SUBFOLDER_CONTENT_BACKUP_FILES "example-data/SQL/backup-files"
ENV JDBC_DATABASE_PORT 5060

# install git
RUN apt-get update -qq && apt-get install -qqy \
  git \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# copy init scripts
COPY clone-data-repo.sh clone-data-repo.sh
COPY setup-atlas.sh setup-atlas.sh


EXPOSE 5060

# clone data repos containing sql dumbs for initalization and start postgres afterwards
CMD  chmod 700 clone-data-repo.sh && ./clone-data-repo.sh && su postgres -c "/usr/local/bin/docker-entrypoint.sh postgres -p 5060"

```
### Backup the DB and Upload it to the Repository
- See https://github.com/aldekal/qc-atlas-content/tree/main?tab=readme-ov-file#how-to
  


## Troubleshooting

- **Port Conflicts**: Ensure no other applications are running on the same ports.


## Haftungsausschluss
Dies ist ein Forschungsprototyp.Die Haftung für entgangenen Gewinn, Produktionsausfall, Betriebsunterbrechung, entgangene Nutzungen, Verlust von Daten und Informationen, Finanzierungsaufwendungen sowie sonstige Vermögens- und Folgeschäden ist, außer in Fällen von grober Fahrlässigkeit, Vorsatz und Personenschäden, ausgeschlossen.

## Disclaimer of Warranty
Unless required by applicable law or agreed to in writing, Licensor provides the Work (and each Contributor provides its Contributions) on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied, including, without limitation, any warranties or conditions of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A PARTICULAR PURPOSE.
You are solely responsible for determining the appropriateness of using or redistributing the Work and assume any risks associated with Your exercise of permissions under this License.