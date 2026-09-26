# World Cities & Universities

Form yapılarında (kayıt, başvuru, profil formları vb.) kullanılmak üzere **ülke → şehir → üniversite** hiyerarşisinde açık veri seti.

An open dataset of **countries → cities → universities** for cascading form selects.

## Ülkeler / Countries

| Ülke | Kod | Şehir | Üniversite | Dosya |
|---|---|---|---|---|
| 🇪🇸 España (Spain) | ES | 52 | 97 | [`dist/es.json`](dist/es.json) |
| 🇹🇷 Türkiye | TR | 81 | 205 | [`dist/tr.json`](dist/tr.json) |

Yeni ülkeler eklenmeye devam ediyor. / More countries coming.

## Klasör Yapısı / Structure

```
world-cities-universities/
├── countries/
│   └── tr/                          ← Ülke
│       ├── country.json
│       └── cities/                  ← Şehirler (her şehir ayrı dosya)
│           ├── 01-adana.json        ← Şehir + o şehirdeki üniversiteler
│           ├── 06-ankara.json
│           └── ...
├── dist/                            ← Hazır kullanım dosyaları (otomatik üretilir)
└── scripts/build.py
```

## Hazır Dosyalar / Files

| Dosya | Açıklama |
|---|---|
| [`dist/countries.json`](dist/countries.json) | Ülke listesi (şehir ve üniversite sayılarıyla) |
| `dist/<ülke-kodu>.json` | Tek ülke: şehirler → üniversiteler (ör. [`dist/tr.json`](dist/tr.json)) |
| [`dist/world.json`](dist/world.json) | Tüm ülkeler tek dosyada |
| [`dist/universities.csv`](dist/universities.csv) | Tüm üniversiteler, düz CSV |
| [`dist/world.sql`](dist/world.sql) | `countries`, `cities`, `universities` tabloları + INSERT'ler |

## Veri Yapısı / Schema

```json
{
  "code": "TR",
  "name": "Türkiye",
  "name_en": "Turkey",
  "cities": [
    {
      "id": "TR-06",
      "name": "Ankara",
      "slug": "ankara",
      "plate_code": 6,
      "region": "İç Anadolu",
      "universities": [
        {
          "id": "tr-ankara-universitesi",
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

- Şehir `id`: [ISO 3166-2](https://en.wikipedia.org/wiki/ISO_3166-2) kodu (ör. `TR-06`)
- Üniversite `id`: `<ülke>-<slug>` — sabittir, liste değişse de değişmez; formlarda değer olarak güvenle kaydedilebilir
- `type`: `state` (devlet), `foundation` (vakıf) veya `private` (özel)
- Ülkeye özel ek alanlar olabilir (ör. Türkiye için `plate_code`, `region`)
- Üniversiteler resmi merkezlerinin (rektörlük) bulunduğu şehirde listelenir

## Kullanım / Usage

**JavaScript (fetch)**

```js
const base = "https://raw.githubusercontent.com/cekubal/world-cities-universities/main/dist";

const countries = await (await fetch(`${base}/countries.json`)).json();
countrySelect.innerHTML = countries.map(c => `<option value="${c.code}">${c.name}</option>`).join("");

countrySelect.onchange = async () => {
  const { cities } = await (await fetch(`${base}/${countrySelect.value.toLowerCase()}.json`)).json();
  citySelect.innerHTML = cities.map(c => `<option value="${c.id}">${c.name}</option>`).join("");
  citySelect.onchange = () => {
    const city = cities.find(c => c.id === citySelect.value);
    uniSelect.innerHTML = city.universities.map(u => `<option value="${u.id}">${u.name}</option>`).join("");
  };
};
```

**SQL**

```bash
sqlite3 app.db < dist/world.sql
```

## Katkı / Contributing

Kaynak veri `countries/<ülke-kodu>/cities/` altındaki şehir dosyalarındadır. `dist/` otomatik üretilir — doğrudan düzenlemeyin.

1. İlgili şehir dosyasını düzenleyin (ör. `countries/tr/cities/34-istanbul.json`)
2. `python3 scripts/build.py` çalıştırın (doğrulama + `dist/` üretimi)
3. Pull request açın

Eksik, kapanmış veya adı değişmiş bir üniversite görürseniz issue açabilirsiniz.

## Kaynaklar / Sources

- España: [RUCT – Ministerio de Ciencia, Innovación y Universidades](https://www.educacion.gob.es/ruct/), [Wikipedia – Anexo:Universidades de España](https://es.wikipedia.org/wiki/Anexo:Universidades_de_Espa%C3%B1a)
- Türkiye: [YÖK](https://www.yok.gov.tr), [Vikipedi – Türkiye'deki üniversiteler listesi](https://tr.wikipedia.org/wiki/T%C3%BCrkiye%27deki_%C3%BCniversiteler_listesi)

## Lisans / License

[MIT](LICENSE)
