- Сохранение -------------------------------------------------------------
🔐 Что нужно сохранить перед удалением
Для ingress-nginx:
Все объекты типа:

bash
Копировать
Редактировать
kubectl get ingress --all-namespaces -o yaml > ingress-backup.yaml
При необходимости: ConfigMap, Service, Deployment из ingress-nginx namespace:

bash
Копировать
Редактировать
kubectl get all,cm,secret -n ingress-nginx -o yaml > ingress-nginx-namespace-backup.yaml
Для Gateway API:
Если ты использовал Gateway/HTTPRoute/TCPRoute:

bash
Копировать
Редактировать
kubectl get gateway,httproute,tcproute,udproute,referencegrant,tlsroute --all-namespaces -o yaml > gateway-api-backup.yaml
💾 Общий бэкап всех ресурсов (если хочешь перестраховаться)
bash
Копировать
Редактировать
kubectl get all --all-namespaces -o yaml > all-resources-backup.yaml
kubectl get crds -o yaml > crds-backup.yaml
kubectl get configmap --all-namespaces -o yaml > configmaps-backup.yaml
kubectl get secrets --all-namespaces -o yaml > secrets-backup.yaml
📦 Сохрани PV/PVC (если использовались):
bash
Копировать
Редактировать
kubectl get pvc,pv --all-namespaces -o yaml > volumes-backup.yaml

Дополнительно (по желанию):
Сделай снимок etcd (если ты хочешь полный state):

bash
Копировать
Редактировать
ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /root/etcd-snapshot.db
Убедись, что запускаешь это с одного из control-plane нод.


- Восстановление --------------------------------------------

Вот как можно восстановить ресурсы Kubernetes из YAML-бэкапов:

📂 Предположим, ты сохранил бэкап в виде файлов:
bash
Копировать
Редактировать
ingress-backup.yaml  
gateway-api-backup.yaml  
ingress-nginx-namespace-backup.yaml  
volumes-backup.yaml  
etc.
🔁 Вариант 1: Восстановление обычных ресурсов (Ingress, Gateway и т.д.)
bash
Копировать
Редактировать
# Применяем все нужные backup-файлы:
kubectl apply -f ingress-backup.yaml
kubectl apply -f gateway-api-backup.yaml
kubectl apply -f ingress-nginx-namespace-backup.yaml
kubectl apply -f volumes-backup.yaml
kubectl apply -f configmaps-backup.yaml
kubectl apply -f secrets-backup.yaml
🔹 Важно: если ты уже удалил ingress-nginx или gateway-api, нужно сначала заново установить их Helm'ом или через Kubespray, затем восстановить ресурсы.

🧠 Вариант 2: Восстановление etcd snapshot (полный state)
Если ты сохранил snapshot:

bash
Копировать
Редактировать
ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot restore /root/etcd-snapshot.db \
  --data-dir /var/lib/etcd-restore
После этого тебе придётся вручную:

Остановить kubelet

Заменить /var/lib/etcd на /var/lib/etcd-restore

Перезапустить kubelet и etcd

⚠️ Это тяжёлая артиллерия — используй только если всё сломалось.

✅ Проверка после восстановления
bash
Копировать
Редактировать
kubectl get ingress --all-namespaces
kubectl get gateway,httproute --all-namespaces
kubectl get pvc,pv --all-namespaces