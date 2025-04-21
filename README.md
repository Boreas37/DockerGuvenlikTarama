# DockerGuvenlikTarama
Sanallaştırma dersi için sunumum.
## 1- Trivy

"Ilk gosterecegim arac Trivy. Bu arac acik kaynakli ve oldukca hizli calisiyor. Docker imajlarini, dosya sistemlerini, bagimliliklari ve yapilandirma dosyalarini tarayabiliyor. Ben simdi imaj taramasi ornegiyle baslayacagim."

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image alpine:3.7
```

"Su anda Trivy, alpine:3.7 imajini tariyor. Bu surum eski oldugu icin icerisinde bilinen zafiyetler mevcut. Trivy bu zafiyetleri CVE veritabanlarina gore cikaracak."

### CIKTI GELINCE

"Burada her satirda bir guvenlik acigi goruyorsunuz. Hemen yaninda bu acigin hangi paketle iliskili oldugu, hangi surumde oldugu ve risk seviyesi var."

"Risk seviyesi genellikle LOW, MEDIUM, HIGH, CRITICAL gibi kategorilere ayrilir. Bu kritik olanlar genelde sistem ele gecirme ya da uzaktan kod calistirma gibi ciddi aciklardir."

### KUBERNETES CONFIG TARAMA

```bash
trivy config deployment.yml
```

"Simdi bir Kubernetes deployment.yaml dosyasini tarayacagim. Bu dosyada `privileged: true` ayari var, yani container root yetkileriyle calisiyor. Trivy bunu 'HIGH' risk olarak rapor edecek."

---

## 2- Grype

"Siradaki aracimiz Grype. Bu arac, container imajlarinin icindeki paketleri tarayarak zafiyetleri listeler. Trivy’ye benzer, ama SBOM desteklemesi gibi bazi teknik farklari var. Simdi kisa bir ornekle gosterecegim."

### SBOM Nedir?

"SBOM, yani *Software Bill of Materials* (Yazilim Malzeme Listesi), bir yazilimin icerisinde hangi bilesenlerin bulundugunu detayli olarak listeleyen makine tarafindan okunabilir bir belgedir. Tipki bir urunun icindekiler listesi gibi, bir yazilimin hangi acik kaynak kutuphaneleri, versiyonlari ve bagimliliklari icerdigini belirtir. Guvenlik ve lisans uyumlulugu gibi konularda seffaflik saglar."

"Grype bu listeyi dogrudan kullanabilir, yani imaji taramadan da elinizdeki bilesen listesine gore zafiyet analizi yapabilirsiniz."

### GRYPE IMAJ TARAMA

```bash
grype alpine:3.7
```

"Bu komut, Alpine Linux 3.7 imajinin icindeki paketleri analiz ediyor. Grype, paket yoneticisinin veritabanina gore hangi CVE’lerin bulundugunu listeliyor."

"Trivy de benzer sekilde CVE listesi verir, ancak Trivy ayrica config dosyalarini ve Kubernetes YAML’larini da tarayabilir. Grype sadece imaj icerigine odaklanir."

### SBOM ile Grype Taramasi

```bash
trivy image --format cyclonedx --output alpine-sbom.json alpine:3.7
grype sbom:alpine-sbom.json
```

"Ilk komut trivy araciyla bir SBOM olusturuyor. Ikinci komut ise Grype’in bu dosyaya gore tarama yapmasini sagliyor. Bu yontem CI/CD boru hatlarina entegre edilmek icin idealdir."

"Grype’in avantaji, SBOM dosyalarini desteklemesi ve ciktisinin sade olmasi. Trivy’ye kiyasla biraz daha gelistirici odakli bir yapi sunuyor."

---

## 3- Docker Scout

"Simdi gosterecegim arac Docker Scout. Bu arac, Docker tarafindan gelistirilmis ve dogrudan Docker CLI’ye entegre edilmis bir guvenlik analiz aracidir."

"Docker Hub’daki imajlar uzerinde guvenlik taramasi yapar ve zafiyetleri dogrudan terminalden gorumlememizi saglar."

### HIZLI GORUNUM

```bash
docker scout quickview alpine:3.7
```

"Bu komutla alpine:3.7 imajinin guvenlik durumunu hizli sekilde gorumleyebiliyorum. 'Quickview' ciktisinda one cikan zafiyetler, imaj katmanlari ve riskli paketler listeleniyor."

### CIKTI GELINCE

"Surada imajda kac adet zafiyet oldugunu goruyoruz. Yanlarinda bu zafiyetlerin kritiklikleri de belirtilmis: ornegin 5 Critical, 12 High gibi."

"Her zafiyetin hangi paketten kaynaklandigi ve hangi imaj katmaninda bulundugu da listeleniyor."

### CVE LISTESI GORUNTULEME

```bash
docker scout cves alpine:3.7
```

"Bu komut ise tam CVE listesini getiriyor. Trivy veya Grype ile benzer sekilde, her bir zafiyetin CVE numarasi, risk seviyesi, ve cozum onerileri gosteriliyor."

"Scout'un avantaji dogrudan Docker CLI icinde yer almasi, ayrica hicbir ek arac yuklemeden hizlica temel guvenlik analizi yapilabilmesidir. Gelistiriciler icin en kolay, hizli ve Docker ortamina entegre bir cozumdur."

---

## 4- Snyk CLI

"Son olarak gosterecegim arac Snyk CLI. Bu arac ozellikle gelistiricilere yonelik olarak gelistirilmistir. Yazilim bagimliliklarini, Docker imajlarini ve kod tabanini tarayabilir. Ayrica hem terminalden hem de web arayuzu uzerinden kullanilabilir."

"Simdi Docker imaji taramasi ornegini gosterecegim."

### IMAJ TARAMASI

```bash
snyk test --docker alpine:3.7
```

"Bu komut, alpine:3.7 imaji uzerinde bir tarama yapiyor. Snyk, imajin icindeki paketleri analiz edip bilinen zafiyetleri listeleyecek. Ek olarak cozum onerileri ve sabit surumler de sunacak."

### CIKTI GELINCE

"Burada listelenen her bir CVE, etkilenen paketle birlikte gosteriliyor. Ornegin: CVE-2019-14697 → musl paketi."

"Yaninda risk seviyesi yaziyor: High, Medium, Low. Ayrica Snyk bu zafiyetin hangi surumde duzeldigini de gosteriyor."

"Snyk’in farki, sadece tespit degil, ayni zamanda cozum onerisi sunmasi. Gelistirici olarak hangi versiyona guncellemeniz gerektigini burada dogrudan gorebiliyorsunuz."

"Snyk ozellikle yazilim projelerine entegre edildiginde cok guclu. Node.js, Java, Python gibi dillerdeki projelerde bagimliliklari da tarayabiliyor. Ayrica CI/CD sistemlerine entegre edip otomatik tarama yapilabilir."

