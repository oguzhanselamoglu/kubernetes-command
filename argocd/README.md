## Argo CD
ArgoCD Continues deployment aracı hali hazırdaki CI ile birlikte kubernetes ortamına deployment için kullanabiliriz


## Kurulum
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
*windows ta base64 desteklenmiyor  pipe dan sonrası silinip aynı şekilde passwordu elde edilebilir
