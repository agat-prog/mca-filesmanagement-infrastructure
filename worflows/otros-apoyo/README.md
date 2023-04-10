
sudo chmod 666 /var/run/docker.sock

Cambios en feature/feature_03


  okteto-deploy:
    name: Deploy into okteto kubernetes
    runs-on: ubuntu-20.04
    needs: [publish_in_dockerhub]
    steps:
      - name: Get Kubeconfig
        uses: okteto/actions/namespace@v1
          id: namespace
          with:
            token: ${{ secrets.OKTETO_TOKEN }}
            namespace: tfm-pre-agat-prog
      - name: Deploy and Wait
        uses: okteto/actions/deploy@v1
          env:
            KUBECONFIG: ${{ steps.namespace.outputs.kubeconfig }}
          with:
            namespace: tfm-pre-agat-prog
            manifest: ../k8s/oauth2.yml
            tag: ${{ secrets.DOCKERHUB_USERNAME }}/$IMAGE_NAME:${{ steps.project.outputs.tag }}
            waitOn: deployment/oauth2
            
            
            
https://blog.santiagoagustinfernandez.com/creando-nuestro-ci-cd-con-github-actions-y-okteto/

CI/CD CON DIAGRAMAS UML
https://davidcampos.org/blog/2020/03/15/k8s-jenkins-example.html


https://www.jenkins.io/doc/book/installing/docker/
            

# Practica 1 - Integración y Entrega Continua

Autor(es): Antonio Gat Alba y Alejandro Molina López

[Repositorio](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd)

## Desarrollo con GitFlow

Una vez creados los workflows y funcionando estos, pasamos a crear la nueva funcionalidad:

```
$ git flow init -d
```

Como la versión base es la 0.1.0, es la que se va a utilizar para desarrollar. Y se creará la release 0.1.0 en lugar de 0.2.0
Subimos develop (si no estaba creado antes) -> Esto desencadena el siguiente Workflow:
- [Workflow2](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530738608)
- [GithubPackages](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/packages/1492930?version=0.1.0-SNAPSHOT)
- [Dockerhub](https://hub.docker.com/layers/238164472/molynx/a.gat.2021-a.molinalop/0.1.0-SNAPSHOT-dev/images/sha256-95fb39520b96af7af4c29b37ae8b5719f632cac3b6a1c4c57aa56ee330290150?context=repo)
- [GithubPackageDocker](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26152505?tag=0.1.0-SNAPSHOT-dev)
```
$ git push --set-upstream origin develop
```

Creamos la nueva rama feature

```
$ git flow feature start add-split-lines
```

Realizamos la feature

- En el pom.xml, poner 0.1.0-SNAPSHOT
- Implementar la funcionalidad -> Copy/Paste

Subimos los cambios

```
$ git add src/test/java/es/urjc/code/daw/library/unitary/LineBreakerUnitaryTest.java
$ git commit -m "Add LineBreaker tests"
$ git add src/main/java/es/urjc/code/daw/library/book/LineBreaker.java
$ git commit -m "Add LineBreaker feature"
$ git add src/main/java/es/urjc/code/daw/library/book/BookService.java
$ git commit -m "Add LineBreaker to BookService"
$ git push --set-upstream origin feature/add-split-lines
```

Esto crea una pull-request (OJO! por defecto intenta mergear a master)

Tras crear el pull request y mergear, se han ejecutado los siguientes workflows:

- [Workflow1](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530804486)

  - Test unitarios y REST (feature/add-split-lines)

- [Workflow2](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530809340)
  - Este workflow ha generado los siguientes artefactos:
    - [GithubPackages](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/packages/1492930?version=0.1.0-SNAPSHOT)
    - [Dockerhub](https://hub.docker.com/layers/238164472/molynx/a.gat.2021-a.molinalop/0.1.0-SNAPSHOT-dev/images/sha256-95fb39520b96af7af4c29b37ae8b5719f632cac3b6a1c4c57aa56ee330290150?context=repo)
    - [GithubPackageDocker](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26152505?tag=0.1.0-SNAPSHOT-dev)  

En local, nos bajamos los cambios:

```
$ git checkout develop && git pull
```

Iniciamos la release 

```
$ git flow release start 0.1.0
```

Borramos "-SNAPSHOT" del pom.xml y ponemos 0.1.0.RC1

Subimos los cambios

```
$ git add pom.xml
$ git commit -m "Preparing release: Set version to 0.2.0"
$ git push -u origin release/0.2.0
```

Se ejecutará el siguiente workflow:

- [Wokflow4](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530856096)
  - Este workflow genera os siguientes artefactos:
  - [GithubPackages](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/packages/1492930?version=0.1.0.RC1)
  - [Dockerhub](https://hub.docker.com/layers/a.gat.2021-a.molinalop/molynx/a.gat.2021-a.molinalop/0.1.0-rc/images/sha256-02a62359a27eb21a9b25d87fb6a5893e2850d8af86fd4de824035361c4170ccb?context=repo)
  - [GithubPackageDocker](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26153590?tag=0.1.0-rc)
    
  - Test unitarios y REST (release/0.1.0)

Una vez consideramos que la release está lista, creamos un nuevo pull request a master.
En este caso, el pull request no genera la ejecución de ningún workflow.
Al mergear, se desencadena el workflow de master:
- [Workflow5](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530877050)
  - Al no haber eliminado del pom la versión RC1 este workflow ha fallado, se ha cambiado a la versión 0.1.0 en master y realizado un push. Esto 
  generará la ejecución, nuevamente, del Workflow5.
- [Workflow5](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530917989)
    Este workflow genera los siguientes artefactos:
  -  [Dockerhub](https://hub.docker.com/layers/a.gat.2021-a.molinalop/molynx/a.gat.2021-a.molinalop/0.1.0/images/sha256-02a62359a27eb21a9b25d87fb6a5893e2850d8af86fd4de824035361c4170ccb?context=repo)
  -  [GithubPackageDocker](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26153590?tag=0.1.0)
```
$ git add pom.xml
$ git commit -m "release version"
$ git push -u origin master
$ git tag 0.1.0
$ git push --tag 
```  

```
$ git checkout develop
```
```
$ git add .
```
```
$ git commit -m "0.2.0 snapshot"
```
```
$ git push
```

- [Workflow2](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530946959)
  - Este workflow ha generado los siguientes artefactos:
    - [GithubPackages](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/packages/1492930?version=0.2.0-SNAPSHOT)
    - [Dockerhub](https://hub.docker.com/layers/a.gat.2021-a.molinalop/molynx/a.gat.2021-a.molinalop/0.2.0-SNAPSHOT-dev/images/sha256-6d2e2a86f6a40d215b869982c6f7cf63462dbc68ac3d659978c121c63877140e?context=repo)
    - [GithubPackageDocker](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26155317?tag=0.2.0-SNAPSHOT-dev)

### Hacemos merge de release/0.2.0 a develop

```
$ git merge release/0.2.0
```
Resolvemos el conflicto
```
$ git add .
```
```
$ git push
```

- [Workflow2](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2530955851)
  - Este workflow ha generado los siguientes artefactos:
  -  [GithubPackages](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/packages/1492930?version=0.2.0-SNAPSHOT)
  -  [Dockerhub](https://hub.docker.com/layers/a.gat.2021-a.molinalop/molynx/a.gat.2021-a.molinalop/0.2.0-SNAPSHOT-dev/images/sha256-6d2e2a86f6a40d215b869982c6f7cf63462dbc68ac3d659978c121c63877140e?context=repo)
  -  [GithubPackageDocker](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26155317?tag=0.2.0-SNAPSHOT-dev)

## Job de Nightly

Todos los días a las 2:00am se ejecuta el job de Nightly 

- [Workflow3](https://github.com/molynx/mca-4.2-a.gat.2021-a.molinalop-2022-cd/actions/runs/2531114882)
  - Test unitarios, REST y Selenium sobre la rama develop
  - [**Dockerhub Hora-nighty**](https://hub.docker.com/layers/a.gat.2021-a.molinalop/molynx/a.gat.2021-a.molinalop/20220620.193041-nightly/images/sha256-9582d018795c224ee6ad45253e02ae6cc9eaa9d06467f3ec7d024a4c4e7544eb?context=repo)
  - [**Dockerhub nighty**](https://hub.docker.com/layers/a.gat.2021-a.molinalop/molynx/a.gat.2021-a.molinalop/nightly/images/sha256-9582d018795c224ee6ad45253e02ae6cc9eaa9d06467f3ec7d024a4c4e7544eb?context=repo)
  - [**GithubPackageDocker Hora-nighty**](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26157898?tag=20220620.193052-nightly)
  - [**GithubPackageDocker nighty**](https://github.com/users/molynx/packages/container/a.gat.2021-a.molinalop/26157898?tag=nightly)