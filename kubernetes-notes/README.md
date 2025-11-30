# 📚 Anotações Kubernetes - devgs8

## Conceitos Fundamentais
- **Pod ≠ Container**: Pod é a menor unidade no Kubernetes
- **Pod**: Agrupamento de containers que compartilham rede e namespace
- Comunicação entre containers no mesmo pod é local
- Dois containers no mesmo pod compartilham 1 endereço IP
## Comandos Úteis

### Listar Pods
- `kubectl get pods`: Lista os Pods no namespace default.
- `kubectl get ns` ou `kubectl get namespaces`: Lista todos os namespaces.
- `kubectl get pods -A`: Lista todos os Pods em todos os namespaces.
- `kubectl get pods -n kube-system`: Lista Pods do namespace kube-system.

### Descrever Pods
- `kubectl describe pods <nome-do-pod>`: Mostra detalhes do Pod.

### Saídas Formatadas
- `kubectl get pods -o yaml`: Saída em YAML.
- `kubectl get pods -o wide`: Mais detalhes (IP, Node, etc.).
- `kubectl get pods -L <label>`: Lista Pods com labels.

### Deletar Pods
- `kubectl delete pods <nome-do-pod>`

### Criar Pods
- `kubectl run <nome> --image <imagem> --port <porta>`: Cria um Pod.
- `kubectl run -ti <nome> --image <imagem>`: Cria um Pod com terminal interativo.

### Executar Comandos no Pod
- `kubectl attach <pod> -c <container> -ti`: Anexa ao terminal do container.
- `kubectl exec -ti <pod> -- <comando>`: Executa comando no Pod (ex: `bash`).

### Simular Criação e Gerar YAML
- `kubectl run <nome> --image <imagem> --dry-run=client -o yaml > pod.yaml`: Gera um YAML para o Pod.

### Aplicar Manifestos
- `kubectl apply -f pod.yaml`: Cria ou atualiza recursos a partir do arquivo.

### Logs
- `kubectl logs <pod>`: Mostra logs do Pod.
- `kubectl logs <pod> -f`: Segue os logs (em tempo real).
- `kubectl logs <pod> -c <container>`: Logs de um container específico.

## Limites de Recursos
- No manifesto do Pod, podemos definir limites (limits) e requisições (requests) de CPU e memória.
- Exemplo:
```yaml
resources:
  limits:
    cpu: "0.5"
    memory: "128Mi"
  requests:
    cpu: "0.3"
    memory: "64Mi"
Volumes - EmptyDir
EmptyDir: Volume temporário que é criado quando o Pod é atribuído a um nó e permanece enquanto o Pod existir.

Útil para compartilhar dados entre containers no mesmo Pod.

Os dados são perdidos quando o Pod é removido.

Exemplo de Estresse com stress
Comando para testar limites: stress --vm-bytes 125M --vm 1 (para memória) e stress --vm 1 --cpu 2 (para CPU).

Notas
Sempre que quisermos alterar algo no Pod, devemos modificar o manifesto YAML e aplicar com kubectl apply -f.

Use kubectl get pods -w para assistir a criação dos Pods.

Use kubectl describe e kubectl logs para debug.

## Comandos Básicos
```bash
kubectl get pods                    # Listar pods
kubectl get pods -A                 # Todos os namespaces
kubectl describe pods <nome>        # Detalhes do pod
kubectl logs <pod> -f               # Logs em tempo real
kubectl exec -ti <pod> -- bash      # Entrar no pod
##Trabalhando com YAML
# Gerar YAML
kubectl run exemplo --image nginx --dry-run=client -o yaml > pod.yaml


# Aplicar
kubectl apply -f pod.yaml
##Execução de Debug
# Entrar em Pods
kubectl attach <pod> -c <container> -ti
kubectl exec -ti <pod> -- bash
kubectl exec -ti <pod> -- sh

# Logs
kubectl logs <pod>
kubectl logs <pod> -f              # Seguir logs em tempo real
kubectl logs <pod> -c <container>  # Logs de container específico



*Documentação criada por devgs8 - $(date)*
