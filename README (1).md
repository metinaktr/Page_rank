# 🕸️ Basit Web Bağlantı Ağı ve PageRank Hesabı

Bu proje, (örnek olarak) **yerel bir Flask sunucusunda** yayımlanan sayfalardan (`/a`, `/b`, `/c`, `/d`, `/e`) giden bağlantıları (outgoing links) toplayarak bir **yönlü grafik (DiGraph)** oluşturur ve **PageRank** algoritmasıyla sayfaların önem skorlarını hesaplar.

Kod, `requests` ve `BeautifulSoup` ile bağlantıları çıkarır; `networkx` ile grafiği kurar ve `nx.pagerank` fonksiyonuyla sonuçları sıralar.

## 📌 Özellikler
- Belirlenmiş URL listesinden `<a href="...">` bağlantılarını çıkarır.
- Yönlü grafik (DiGraph) oluşturur ve kenarları ekler.
- **PageRank** değerlerini hesaplar ve en yüksek skorlu ilk 4 sonucu yazdırır.
- Tüm düğümlerin sıralı PageRank listesini konsola basar.

## 🛠 Kullanılan Teknolojiler
- **Python 3.x**
- **requests** — HTTP istekleri
- **beautifulsoup4** — HTML parsing
- **networkx** — Grafik modelleme ve PageRank

## 📂 Dosya Yapısı
```
├── pagerank_example.py   # Ana Python betiği (örnek kod)
└── README.md             # Proje açıklaması (bu dosya)
```

## ⚙️ Kurulum
Gerekli paketleri yükleyin:

```bash
pip install requests beautifulsoup4 networkx
```

> Not: Kodunuz `http://127.0.0.1:5000` üzerinde çalışan bir sunucuya ihtiyaç duyar. Örneğin basit bir Flask uygulamasıyla `/a`, `/b`, `/c`, `/d`, `/e` rotalarını ve aralarında linkleri tanımlayabilirsiniz.

### Örnek Flask Sunucusu (isteğe bağlı)
Aşağıdaki minimal örnek, beş sayfa arasında bağlantılar kurar:

```python
from flask import Flask
app = Flask(__name__)

pages = {
    'a': ['http://127.0.0.1:5000/b', 'http://127.0.0.1:5000/c'],
    'b': ['http://127.0.0.1:5000/c', 'http://127.0.0.1:5000/d'],
    'c': ['http://127.0.0.1:5000/a'],
    'd': ['http://127.0.0.1:5000/e'],
    'e': ['http://127.0.0.1:5000/a']
}

html_tpl = lambda links: '<br>'.join([f'<a href="{u}">{u}</a>' for u in links])

@app.route('/<p>')
def page(p):
    links = pages.get(p, [])
    return html_tpl(links) or 'No links'

if __name__ == '__main__':
    app.run(port=5000, debug=True)
```

## ▶️ Çalıştırma
1. (Varsa) Flask sunucunuzu başlatın: `python app.py`
2. PageRank betiğini çalıştırın:

```bash
python pagerank_example.py
```

## 📊 Beklenen Çıktı
- Konsolda en üst 4 sayfanın PageRank skorları ve tüm sayfaların sıralı listesi görüntülenir.

Örnek çıktı (temsilî):
```
[("http://127.0.0.1:5000/c", 0.242), ("http://127.0.0.1:5000/a", 0.216), ("http://127.0.0.1:5000/b", 0.205), ("http://127.0.0.1:5000/d", 0.172)]
[("http://127.0.0.1:5000/c", 0.242), ("http://127.0.0.1:5000/a", 0.216), ("http://127.0.0.1:5000/b", 0.205), ("http://127.0.0.1:5000/d", 0.172), ("http://127.0.0.1:5000/e", 0.165)]
```

## 🔧 Notlar ve İyileştirmeler
- **Bağlantı Normalizasyonu:** Göreli URL'ler (`/b` gibi) varsa `urllib.parse.urljoin` ile mutlak URL'ye çevrilmelidir.
- **Ağırlıklı Kenarlar:** Eğer sayfalar arası bağlantı sıklığı veya önem farklıysa, kenarlara ağırlık ekleyerek `nx.pagerank(G, weight="weight")` parametresini anlamlı hale getirebilirsiniz.
- **Hata Yönetimi:** `requests.get` için zaman aşımı (`timeout`), bağlantı hataları (`try/except`) ve `response.status_code` kontrolleri ekleyin.
- **Görselleştirme (opsiyonel):** Ağı çizmek için `matplotlib` ile basit bir görselleştirme yapabilirsiniz:

```python
import matplotlib.pyplot as plt
pos = nx.spring_layout(G, seed=42)
nx.draw(G, pos, with_labels=True, node_color='#4c72b0', edge_color='#888', 
        node_size=800, font_size=8)
plt.title('Yerel Sayfalar Arası Bağlantı Ağı')
plt.show()
```

## 📜 Lisans
Bu örnek proje MIT Lisansı ile yayımlanabilir. Dilediğiniz gibi kullanıp genişletebilirsiniz.
