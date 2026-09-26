# Turkey Cities & Universities

Form yapılarında (kayıt, başvuru, profil formları vb.) kullanılmak üzere **Türkiye'nin 81 ili ve her ildeki üniversitelerin** hazır veri seti.

A ready-to-use dataset of **Turkey's 81 provinces and the universities in each**, for cascading form selects (country → city → university).

`Ülke → Şehir → Üniversite`

**81 il · 205 üniversite** (131 devlet, 74 vakıf) — askeri/güvenlik akademileri dahil, meslek yüksekokulları hariç.

## Klasör Yapısı / Structure

```
turkey-cities-universities/
├── country.json              ← Ülke (Türkiye)
├── cities/                   ← Şehirler (her il ayrı dosya, plaka koduna göre)
│   ├── 01-adana.json         ← Adana ve Adana'daki üniversiteler
│   ├── 06-ankara.json
│   ├── 34-istanbul.json
│   └── ...                   (81 il)
├── dist/                     ← Hazır kullanım dosyaları (otomatik üretilir)
└── scripts/build.py
```

Her şehir dosyası / Each city file:

```json
{
  "plate_code": 1,
  "name": "Adana",
  "region": "Akdeniz",
  "universities": [
    { "name": "Çukurova Üniversitesi", "type": "state", "website": "https://www.cu.edu.tr" }
  ]
}
```

## Hazır Dosyalar / Files

| Dosya | Açıklama |
|---|---|
| [`dist/turkey.json`](dist/turkey.json) | İç içe yapı: ülke → iller → üniversiteler |
| [`dist/cities.json`](dist/cities.json) | Sadece iller (plaka kodu, ad, slug, bölge) |
| [`dist/universities.json`](dist/universities.json) | Düz üniversite listesi (`city_id` ile) |
| [`dist/universities.csv`](dist/universities.csv) | CSV formatı |
| [`dist/turkey.sql`](dist/turkey.sql) | `cities` ve `universities` tabloları + INSERT'ler |

## Veri Yapısı / Schema

```json
{
  "country": { "code": "TR", "name": "Türkiye", "name_en": "Turkey" },
  "cities": [
    {
      "id": 6,
      "plate_code": 6,
      "name": "Ankara",
      "slug": "ankara",
      "region": "İç Anadolu",
      "universities": [
        {
          "id": 12,
          "name": "Ankara Üniversitesi",
          "slug": "ankara-universitesi",
          "type": "state",
          "website": "https://www.ankara.edu.tr"
        }
      ]
    }
  ]
}
```

- `id` (şehir) = plaka kodu
- `type`: `state` (devlet) veya `foundation` (vakıf)

## Kullanım / Usage

**JavaScript (fetch)**

```js
const url = "https://raw.githubusercontent.com/cekubal/turkey-cities-universities/main/dist/turkey.json";
const { cities } = await (await fetch(url)).json();

citySelect.innerHTML = cities.map(c => `<option value="${c.id}">${c.name}</option>`).join("");
citySelect.onchange = () => {
  const city = cities.find(c => c.id == citySelect.value);
  uniSelect.innerHTML = city.universities.map(u => `<option value="${u.id}">${u.name}</option>`).join("");
};
```

**SQL**

```bash
sqlite3 app.db < dist/turkey.sql
```

## Katkı / Contributing

Kaynak veri `cities/` altındaki il dosyalarındadır. `dist/` klasörü otomatik üretilir — doğrudan düzenlemeyin.

1. İlgili il dosyasını düzenleyin (ör. `cities/34-istanbul.json`)
2. `python3 scripts/build.py` çalıştırın (doğrulama + `dist/` üretimi)
3. Pull request açın

Eksik, kapanmış veya adı değişmiş bir üniversite görürseniz issue açabilirsiniz. Güncel resmi liste için: [YÖK Atlas](https://yokatlas.yok.gov.tr) / [YÖK](https://www.yok.gov.tr).

## Lisans / License

[MIT](LICENSE)
