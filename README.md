# ChatGPT Sağ Tık Menüsü — 1.2.0

Hazırlanma tarihi: **3 Kasım 2026**

**1.2.0 düz menü:** Kademeli alt menü (ChatGPT → …) kaldırıldı. Klasör ve boş alanda doğrudan **Codex Çalışması Oluştur** görünür. Kur / Güncelle, 1.1.x alt menü kayıtlarını da temizler. OpenAI simgesi aynı kaynaktan gelir (`OpenAI-1.2.0.ico`).

**1.1.2 yeniden kurulum düzeltmesi (geçerli):** Manifestsiz bilinen eski EXE hash’leriyle güvenli yeniden kurulum; bilinmeyen dosyaların üzerine yazılmaz.

## Kurulum

1. **ChatGPTMenu-Kurulum.exe** dosyasını açın.
2. **Kur / Güncelle** düğmesine basın ve Windows yönetici onayını kendiniz verin.
3. Bir klasöre veya klasörün içindeki boş alana sağ tıklayın.
4. **Codex Çalışması Oluştur** seçeneğini kullanın.

Bu sürüm Program Files'a ve makine kapsamındaki menüye kurulur. Yönetici onayı gerekir. Önceki 1.0.x sürümlerinin mevcut kullanıcıya ait paket kayıtları ve eski program dosyaları yedeklenerek emekliye ayrılır. Diğer uygulamaların menülerine dokunulmaz.

Kurulumdan önce tanılama penceresi hâlâ açıksa **Kaldır ve kapat** ile geçici Not Defteri test girişini temizleyin.

Windows 11'in kısa menüsünde **Daha fazla seçenek göster** gerekebilir. Hedef, paylaştığınız klasik sağ tık menüsüdür. Sistem genelindeki menü tercihleri değiştirilmez; kurulum Explorer'ı kendiliğinden yeniden başlatmaz.

## Kullanım

Arnavutköy klasöründe menüye tıkladığınızda **Arnavutköy klasörü** adlı boş bir Codex sohbeti açılır. Sohbet seçilen klasörde çalışır. Her seçim yeni sohbet açar. Otomatik mesaj gönderilmez.

Bu sürüm yeni proje kartı oluşturmaz veya sohbetin proje üyeliğini yazmaz. Çalışma klasörünü ve sohbet adını ayarlar. Dosya tarama, değiştirme, yükleme ve ChatGPT'ye dosya ekleme yoktur. **ChatGPT Sohbetine Ekle** sonraki faza ayrılmıştır.

## Gereksinimler

- Windows 10/11, .NET Framework 4.8.
- Kurulum/kaldırma için Windows yönetici onayı. Günlük menü kullanımı normal kullanıcı yetkisiyle çalışır.
- OpenAI masaüstü uygulaması ve codex:// protokolü kurulu, kullanıcının Codex hesabı açık olmalı.
- Yerel Codex aracı bulunmalı: önce kullanıcının %LOCALAPPDATA%\Programs\OpenAI\Codex\bin\codex.exe yolu, ardından PATH aranır.
- Yerel klasörler desteklenir; UNC/ağ yolları kabul edilmez. OneDrive klasörü erişilebilir olmalıdır.

Makine kapsamındaki menü tüm kullanıcılara görünür; her kullanıcı kendi Codex kurulumunu ve hesabını kullanır. İlk kurulum aynı Windows kullanıcısının yönetici onayıyla yapılmalıdır; farklı bir yönetici hesabıyla UAC kimliği değiştirme bu sürümde test edilmedi.

Yerel sohbet protokolü **codex-cli 0.150.1** ile test edildi. Arayüz deneyseldir; Codex güncellemelerinde yeniden doğrulama gerekebilir. Ek bağımlılık indirilmez. Dosya dijital imzasızdır; kurumun çalıştırma politikaları geçerlidir. Paket resmî OpenAI ürünü değildir.

## Kaldırma ve geri dönüş

Kurulum dosyasını yeniden açıp **Kaldır** seçeneğini kullanın ve yönetici onayını verin. Alternatif: Windows Yüklü uygulamalar → ChatGPT Sağ Tık Menüsü → Kaldır.

Kurulum dosyasını indirdiğiniz konumdan çalıştırarak kaldırmak en temiz yöntemdir. Program Files'taki çalışan EXE kendisini silemezse EXE ve kaldırılmış durumunu belirten sahiplik manifesti geride kalabilir; dışarıdaki kurulum dosyasıyla Kaldır işlemi tamamlanabilir. Bu işaret, sonraki kurulumun dosyayı güvenle tanımasını sağlar.

Sohbetler, çalışma dosyaları ve kullanıcı logları silinmez. Yedekler %ProgramData%\ChatGPTContextMenu\Backups altında korunur.

## Kurulum sonrası kontrol

1. Arnavutköy klasörünün boş alanında **Codex Çalışması Oluştur** görünmeli.
2. Menü eylemi **Arnavutköy klasörü** adlı sohbeti açmalı.
3. Sohbete “Yalnızca Get-Location ile çalışma klasörünü bildir; dosya değiştirme.” yazıp gönderin.
4. Tam yol seçtiğiniz klasörle aynı olmalı.
5. Klasör simgesine sağ tıklayarak da menüyü kontrol edin.

Hata kaydı kullanıcının %LOCALAPPDATA%\ChatGPTContextMenu\menu.log dosyasındadır.

## Neden 1.1.0?

1.0.0 ve 1.0.1 sürümlerinin kullanıcı kapsamındaki menü kayıtları bu bilgisayarda görünmedi. Onaylı testte en basit kullanıcı girişi de görünmedi; makine kapsamındaki aynı tür giriş kullanıcı tarafından görüldü ve Not Defteri'ni açtı. Bu bulguya göre 1.1.0 makine kapsamına taşındı. İşletim sisteminin kullanıcı kayıtlarını neden göstermediği kesinleştirilmiş değildir.

1.1.0 bu bilgisayarda kuruldu. Kullanıcı ChatGPT alt menüsünün ve seçeneğin göründüğünü onayladı. Kurulu menü komutu normal kullanıcı yetkisiyle ayrıca çalıştırıldı; Arnavutköy klasörü adlı boş sohbet ve doğru çalışma yolu doğrulandı. Ayrıntılı kabul ve test sınırları teknik rehberdedir.

Bu GitHub deposu **yalnızca dağıtım paketidir** (kurulum EXE’si, README, bütünlük özeti). Kaynak kod bu repoda yoktur. Kurulum, güvenlik, yedek, geri alma ve sürüm geçmişi için bkz. docs/2026-11-03-kurulum-ve-isletim.md.

**Bütünlük kontrolü (SHA-256):** İndirdiğiniz `ChatGPTMenu-Kurulum.exe` dosyasının özeti `SHA256SUMS.txt` içindeki satırla aynı olmalıdır. PowerShell: `Get-FileHash -LiteralPath .\ChatGPTMenu-Kurulum.exe -Algorithm SHA256`.
