                          +-------------------------+
                          |     Programador         |
                          |   StressApp (250MB)     |
                          +------------+------------+
                                       |
                                       v
                     +-----------------------------+
                     |        Pod YAML             |
                     +-----------------------------+
                     | requests: 100Mi / 250Mi     |
                     | limits:   200Mi / 280Mi     |
                     +-----------------------------+
                                       |
                                       v
         +------------------------- Scheduler ---------------------------+
         |                                                               |
         |  1. Verifica se o nó tem os "requests" disponíveis           |
         |  2. Coloca o Pod num nó com recursos suficientes             |
         +-------------------------------+-------------------------------+
                                         |
                                         v
                      +-------------------------------------+
                      |                 Nó                   |
                      |     (CPU, RAM, outros Pods)          |
                      +------------------+--------------------+
                                         |
                                         v
                      +-------------------------------------+
                      |          Execução do Pod            |
                      +-------------------------------------+
                      |  Stress tenta usar 250MB de RAM      |
                      |                                      |
                      |  SE uso > limit → OOMKilled          |
                      |  SE uso <= limit → Running           |
                      +-------------------------------------+




🚀 Treinamento CH2 — Gestão de Recursos no Kubernetes
Desafio: Controlar consumo de memória com a aplicação StressApp
🎯 Contexto

Como Engenheiro DevOps, recebeste a tarefa de implementar uma nova aplicação chamada StressApp, responsável por processamento intenso de dados. Os programadores informaram que esta aplicação tende a consumir bastante memória RAM.

O objectivo é garantir que ela seja executada dentro do cluster Kubernetes sem comprometer a estabilidade do nó nem prejudicar outras aplicações.
Para isso, irás trabalhar com:

Pods

Requests e Limits

Controlo de memória (OOMKilled)

Namespaces

🧪 Primeiro teste: limites insuficientes
YAML inicial
resources:
  limits:
    cpu: "0.5"
    memory: "200Mi"
  requests:
    cpu: "0.3"
    memory: "100Mi"
command: ["stress"]
args: ["--vm","1","--vm-bytes","250M","--vm-hang","1"]

🔍 Resultado da execução

Comando:

kubectl get pods


Output:

treinamento-ch2   0/1   CrashLoopBackOff   1 (14s ago)   18s


Comando:

kubectl describe pod treinamento-ch2


Pontos relevantes:

Last State:  Terminated
Reason:      OOMKilled
Warning:     BackOff restarting failed container

📌 Análise

O container estava configurado para utilizar ~250MB, mas o limit definido no Pod era apenas 200Mi.
Resultado: O Kubernetes mata o processo por falta de memória, levando a um CrashLoopBackOff.

✅ Solução: ajustar corretamente os recursos
YAML corrigido
resources:
  limits:
    cpu: "0.5"
    memory: "280Mi"
  requests:
    cpu: "0.3"
    memory: "250Mi"
command: ["stress"]
args: ["--vm","1","--vm-bytes","250M","--vm-hang","1"]


⚙️ Agora o limite suporta a quantidade real de memória solicitada pela aplicação.

🔎 Resultado após correção

Comando:

kubectl get pods


Output:

treinamento-ch2   1/1   Running   0   47m


Comando:

kubectl describe pod treinamento-ch2


Eventos:

Normal  Created   kubelet  Created container stress
Normal  Started   kubelet  Started container stress
Status: Running

🎉 Aplicação estável e com os recursos adequados

🎉OOMKilled devido a limites insuficientes
apiVersion: v1
kind: Pod
metadata:
  name: stressapp-falha
  labels:
    app: stressapp
spec:
  containers:
    - name: stress
      image: polinux/stress
      resources:
        limits:
          cpu: "0.5"
          memory: "200Mi"
        requests:
          cpu: "0.3"
          memory: "100Mi"
      command: ["stress"]
      args: ["--vm","1","--vm-bytes","250M","--vm-hang","1"]

🎉corrigido, com limites compatíveis com o consumo da aplicação.

apiVersion: v1
kind: Pod
metadata:
  name: stressapp-sucesso
  labels:
    app: stressapp
spec:
  containers:
    - name: stress
      image: polinux/stress
      resources:
        limits:
          cpu: "0.5"
          memory: "280Mi"
        requests:
          cpu: "0.3"
          memory: "250Mi"
      command: ["stress"]
      args: ["--vm","1","--vm-bytes","250M","--vm-hang","1"]

