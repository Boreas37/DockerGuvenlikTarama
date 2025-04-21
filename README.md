# DSEC Güvenlik Araçları Tanıtımı

## 1. Trivy

Trivy, açık kaynaklı ve hızlı bir güvenlik tarama aracıdır. Docker imajlarını, dosya sistemlerini, bağımlılıkları ve yapılandırma dosyalarını tarayabilir.

### Docker İmaj Taraması

```bash
docker run aquasec/trivy image <taranacak_imaj>
```

Örnek: `alpine:3.7` imajı tarandığında, bu sürümdeki bilinen güvenlik açıkları CVE veritabanlarına göre listelenir.

Çıktıda:
- Her satırda bir güvenlik açığı yer alır.
- Açığın ilgili olduğu paket, sürüm bilgisi ve risk seviyesi (LOW, MEDIUM, HIGH, CRITICAL) gösterilir.
- Kritik seviyeler genellikle sistem ele geçirme veya uzaktan kod çalıştırma gibi ciddi açıklardır.

### Kubernetes Config Taraması

```bash
./trivy.exe config deployment.yml
```

Örneğin `deployment.yaml` içinde `privileged: true` ayarı varsa, Trivy bunu "HIGH" risk olarak raporlar.

---

## 2. Grype

Grype, container imajları içindeki paketleri tarayarak zafiyetleri listeler. Trivy’ye benzer işlevsellik sunar ancak bazı teknik farklılıkları vardır.

### SBOM Nedir?

**SBOM (Software Bill of Materials)**, bir yazılımın içinde bulunan tüm bileşenlerin, bağımlılıkların ve kütüphanelerin listelendiği bir dökümandır. Yazılımın “içindekiler listesi” olarak düşünülebilir. SBOM, yazılım bileşenlerinin şeffafı bir şekilde görülmesini ve güvenlik açıklarının daha etkin bir şekilde izlenmesini sağlar.

Grype, bu SBOM dosyalarını doğrudan analiz edebilir.

### Kullanım

```bash
./grype.exe <taranacak_imaj>
```

Ayrıca SBOM ile tarama yapmak için:

```bash
./trivy.exe image --format cyclonedx --output alpine-sbom.json alpine:3.7
./grype.exe sbom:alpine-sbom.json
```

---

## 3. Docker Scout

Docker Scout, Docker tarafından geliştirilmiş ve Docker CLI’ye entegre edilmiş bir güvenlik analiz aracıdır.

### Hızlı Tarama

```bash
docker scout quickview alpine:3.7
```

Bu komut, zafiyetleri, imaj katmanlarını ve riskli paketleri listeler.

### Detaylı CVE Listesi

```bash
docker scout cves alpine:3.7
```

Scout'un özelliği, Docker ortamına doğrudan entegre çalışması ve ek bir aracın kurulumuna gerek kalmadan analiz yapabilmesidir.

---

## 4. Snyk CLI

Snyk CLI, geliştiricilere yönelik bir güvenlik aracıdır. Docker imajlarını, yazılım bağımlılıklarını ve kod tabanlarını analiz edebilir.

### Docker İmaj Taraması

```bash
./snyk-win.exe test --docker alpine:3.7
```

Taramada, CVE’ler, ilgili paketler ve çözümler sunulur.

Örneğin:
- CVE-2019-14697 → musl paketi
- Risk seviyeleri: High, Medium, Low
- Hangi sürümlerde düzeltildiği belirtilir

Snyk, Node.js, Java, Python gibi projelerde de kullanılabilir ve CI/CD sistemlerine entegre edilebilir.

