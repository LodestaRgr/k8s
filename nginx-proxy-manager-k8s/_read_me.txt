--- install
kubectl apply -f namespace.yaml
kubectl apply -f pvc-data.yaml
kubectl apply -f pvc-lets.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml


--- Rolling update образа
kubectl -n nginx-proxy-manager set image deploy/nginx-proxy-manager npm=jc21/nginx-proxy-manager:<new-tag>

--- Выключить NPM
kubectl -n nginx-proxy-manager scale deploy nginx-proxy-manager --replicas=0

---Включить NPM
kubectl -n nginx-proxy-manager scale deploy nginx-proxy-manager --replicas=1

---Бэкап (после выключения)
kubectl -n nginx-proxy-manager exec -it npm-backup -- tar czf /tmp/backup.tar.gz -C / data etc/letsencrypt

---Восстановление	tar xzf /tmp/backup.tar.gz -C / && chown -R 1000:1000 /data /etc/letsencrypt внутри restore-pod

--- Скопировать на ноду 1
scp /tmp/npm-backup-20250527.tar.gz     lodestargr@172.20.0.71:/tmp/
