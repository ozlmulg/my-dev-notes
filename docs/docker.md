# Docker Cheatsheet

## Temel Komutlar
```bash
docker ps           # Çalışan container'ları listeler
docker images       # Image'leri listeler
docker run -it --rm ubuntu bash  # Ubuntu container başlatır
```

## İmaj Yönetimi
```bash
docker build -t myapp .
docker pull nginx
docker push myapp
```
