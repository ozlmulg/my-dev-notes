# My Dev Notes

Bu repo, benim kişisel geliştirme notlarım, cheatsheet’lerim, öğrendiğim yeni konular ve okunacak mesleki kitaplardan oluşan dijital not defterimdir.

## İçerik

- Docker, Kubernetes, iOS ve diğer teknolojilerle ilgili cheatsheet’ler  
- Geliştirme notları ve öğrendiğim yeni bilgiler  
- Okunacak kitap listeleri  
- Kişisel projeler ve fikirler

## Kurulum ve Kullanım

1. Repoyu klonla:
   ```bash
   git clone https://github.com/kullaniciAdi/my-dev-notes.git
   cd my-dev-notes
   ```

2. Sanal ortam oluştur ve aktif et:
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
    ```

3. Gerekli paketleri yükle:
   ```bash
   pip install mkdocs-material
   ```

4. Siteyi yerel olarak başlat:
   ```bash
   mkdocs serve
   ```

Tarayıcıda http://127.0.0.1:8000 adresine git.

5. Siteyi GitHub Pages’e deploy etmek için:
   ```bash
   mkdocs gh-deploy
   ```