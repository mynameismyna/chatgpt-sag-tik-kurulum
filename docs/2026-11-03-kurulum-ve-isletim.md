# ChatGPT klasör menüsü — kurulum ve işletim

**Tarih:** 3 Kasım 2026. **Güncel sürüm:** 1.2.0 (1.1.x kayıtları aşağıda tarihsel referans). Aşağıdaki 1.1.0 kayıtları önceki başarılı kurulum kabulüne, 1.1.1 kayıtları simge güncellemesinin ilk denemesine aittir.

**GitHub dağıtım deposu:** Bu repoda yalnızca kurulum EXE’si ve dokümantasyon yayınlanır; `source/` kaynak kodu repoda yoktur (yerel geliştirme kopyasında kalır). Aşağıdaki `source/` maddeleri tam paketin bileşen listesini anlatır.

## İşlem özeti

Amaç, klasik Windows klasör menüsüne doğrudan **Codex Çalışması Oluştur** eklemek; seçilen klasörde tam başlıklı boş sohbet açmaktır. Menü hem klasör simgesinde hem klasör boş alanında hedeflenir. ChatGPT Sohbetine Ekle bu fazın dışında kalır. 1.2.0 ile kademeli alt menü kaldırıldı.

1.0.x kullanıcı kapsamındaki kayıtlar bu bilgisayarda görünmedi. Kontrollü tanılama makine kapsamındaki düz menünün göründüğünü ve çalıştığını gösterdi. Bu nedenle 1.1.0 makine kapsamına taşınmıştır. Kullanıcı kayıtlarının görünmemesinin işletim sistemi seviyesindeki kesin nedeni kanıtlanmamıştır.

## Dosyalar ve etkilenen bileşenler

- ChatGPTMenu-Kurulum.exe: tek dosyalı kurulum, kaldırma ve sohbet açma programı.
- source/ChatGPTMenu.cs: C# kaynak kodu.
- source/app.manifest: normal çalışmada asInvoker. Yalnız kurulum/kaldırma alt süreci runas ile yükseltilir.
- source/OpenAI.ico ve source/ICON-SOURCE.md: değiştirilmemiş resmî logo kaynağı ve kaynağın hash/provenans kaydı. 1.1.1'de hem EXE'ye gömülür hem sürümlü ICO olarak kurulur.
- source/Build.ps1: sistemdeki .NET Framework C# derleyicisiyle derleme.
- source/Test.ps1: kayıt defterine yazmadan kontroller.
- README.md: kullanıcı rehberi. SHA256SUMS.txt: paket dosyalarının bütünlük özetleri.
- Ayrı menu-tanilama çıktısı geçici, yönetici onaylı düz menü karşılaştırma aracıdır; kalıcı paketin parçası değildir.
- Önceki Arnavutköy inceleme raporuna yeni protokol ve paket aşaması eklenmiştir.

İlgili Windows bileşenleri Explorer, makine/kullanıcı sınıf kayıtları ve UAC'dir. Codex yalnız yerel sohbet protokolüyle kullanılır. Sunucu, Windows servisi, Scheduled Task, IIS, API servisi, veritabanı, DNS, firewall veya sertifika değişikliği yoktur. Harici bağımlılık kurulmaz.

## Gerçek davranış ve çağrı akışı

1. Explorer seçilen klasörü EXE'ye /open argümanıyla verir. Yol kabuk kodu olarak çalıştırılmaz.
2. EXE tam yerel yolu normalleştirir; boş/göreli/UNC veya olmayan klasörü reddeder.
3. Kullanıcıya ait codex.exe bulunur; app-server --stdio penceresiz ve geçici alt süreç olarak çalıştırılır. TCP portu açılmaz.
4. UTF-8 JSON ile initialize/initialized; ardından thread/start {cwd, ephemeral:false} kullanılır.
5. thread/name/set ile tam <Klasör Adı> klasörü başlığı atanır.
6. thread/read {includeTurns:false} ile ad ve çalışma yolu karşılaştırılır.
7. codex://threads/<id> ile oluşturulan sohbet masaüstü uygulamasında açılır. Yerel alt süreç kapatılır.

turn/start çağrılmaz; model turu veya otomatik komut yürütme talep edilmez. Oluşan sohbet boş ve kalıcıdır. Codex kendi başlangıç yapılandırmasını/eklenti altyapısını normal biçimde yükleyebilir. Paket global Codex ayarını, model tercihini, sandbox veya onay politikasını değiştirmez.

Yeni proje kartı oluşturulmaz, proje üyeliği yazılmaz. Aynı klasörde tekrar seçmek yeni sohbet oluşturur. Klasör içeriği bu eylemle taranmaz veya yüklenmez. Sonraki mesajlarla istenen işler kullanıcının Codex izinlerine tabidir.

## Kurulum kapsamı ve ön koşullar

Windows 10/11 ve .NET Framework 4.8; OpenAI masaüstü uygulaması, kayıtlı codex:// şeması, açık Codex hesabı ve kurulu CLI gerekir. Yerel protokol codex-cli 0.150.1 ile doğrulanmıştır; başka sürümler ve makineler ayrıca test edilmelidir.

Program, kurulum penceresinde Kur / Güncelle seçilince kendi /install-machine alt sürecini Windows runas ile başlatır. Kullanıcı UAC onayını kendisi verir. Yükseltilmiş giriş RequireAdmin kontrolü yapar. Günlük /open eylemi asInvoker çalışır.

Kurulum yeri: %ProgramFiles%\ChatGPTContextMenu\ChatGPTMenu.exe.
Güncel menü simgesi: aynı dizindeki OpenAI-1.1.2.ico. Üst/alt menü Icon değerleri bu tam yola ve 0 simge indeksine işaret eder.
Makine manifesti: aynı dizindeki manifest.json.
Kullanıcı logları: %LOCALAPPDATA%\ChatGPTContextMenu\menu.log.
Yedekler: %ProgramData%\ChatGPTContextMenu\Backups\yyyyMMdd-HHmmss-fff.

Kurulum aynı hesabın yönetici onayıyla yapılmalıdır. Başka yönetici kimliğiyle UAC onayı verilirse HKCU kapsamı o hesaba ait olacağından eski kullanıcı temizliği ve CLI bulma davranışı doğrulanmış değildir; bu biçim test edilmedi.

Menü tüm kullanıcılara görünür. Her kullanıcı günlük kullanımda kendi Codex yolunu/hesabını kullanır. Codex'i olmayan kullanıcıya anlamlı hata gösterilir. Ortam değişkeni, servis veya restart gereksinimi eklenmez. Explorer'ın yeniden başlatılması otomatik yapılmaz.

## Kayıt defteri planı ve geçiş

HKLM altında 1.2.0 paket kökleri:

~~~text
Software\Classes\Directory\shell\ChatGPT.CodexContextMenu
Software\Classes\Directory\Background\shell\ChatGPT.CodexContextMenu
Software\Microsoft\Windows\CurrentVersion\Uninstall\ChatGPT.CodexContextMenu
~~~

Her iki shell anahtarında MUIVerb=Codex Çalışması Oluştur; komut doğrudan `\command` altındadır (ExtendedSubCommandsKey yok). Kur / Güncelle, 1.1.x `ChatGPT.CodexDirectoryCommands` ve `ChatGPT.CodexBackgroundCommands` sahipli kayıtlarını kaldırır.

~~~text
"<Program Files tam yolu>\ChatGPTContextMenu\ChatGPTMenu.exe" /open "%1\."
"<Program Files tam yolu>\ChatGPTContextMenu\ChatGPTMenu.exe" /open "%V\."
~~~

%1 klasör simgesidir; %V klasör boş alanıdır. Sondaki \. sürücü kökünde tırnak öncesi ters eğik çizgi sorununu önlemek içindir; yol normalleştirilir. Tek klasör seçimi test kapsamıdır; toplu seçim doğrulanmadı.

1.0.x'ten geçişte mevcut kullanıcıya ait HKCU altındaki aynı beş kök, yalnız sahiplik belirteci eşleşirse kaldırılır. Eski kullanıcı EXE'si ve manifesti sahipliği doğrulanıp yedeğe alındıktan sonra kaldırılır. Kullanıcı logları ve eski yedekler korunur. HKCU kayıtları HKLM'yi gölgelememelidir. Başka uygulamaların veya yabancı sahiplikteki anahtarların üzerine yazılmaz.

Kurulum sırası: ön koşul ve sahiplik kontrolü → iki hive anlık görüntüsü ve eski dosya yedekleri → Program Files'a kopya → eski sahipli yapıların temizliği → HKLM planı → makine manifesti → eski dosyaların kaldırılması → log ve SHChangeNotify(SHCNE_ASSOCCHANGED).

## Güvenlik ve izlenebilirlik

Kurulum/kaldırma makine kaydına yazdığı için yönetici onayı gerekir. Günlük kullanım yükseltilmez. Program Files standart Windows izinleriyle ortak EXE'yi korur. Sistem güvenlik politikaları, Defender, ExecutionPolicy ve UAC tercihleri değiştirilmez; Windows güvenlik iletişim kutuları otomatik yanıtlanmaz.

Token, parola, API anahtarı veya connection string pakette yoktur; kimlik doğrulama Codex'e aittir. Dosya eki ve clipboard kullanımı yoktur. JSON ile yol aktarılır; cmd /c, dinamik PowerShell komutu veya eval kullanılmaz. Sunucu stderr çıktısı kimlik bilgisi riski nedeniyle loga kopyalanmaz. Hata kullanıcıya gösterilir; yarım oluşturulmuş sohbet varsa kimliği bildirilir ve otomatik silinmez.

Log: zaman ve saat dilimi ofseti, sonuç, sohbet kimliği ve başlığı. Başlık kurum içi veri içerebilir; log yerelde kalır. 512 KiB üzerinde log döndürülür; eski loglar otomatik silinmez. Log yazma hatası uyarı olarak gösterilir. EXE imzasızdır; kurumun çalıştırma politikaları aşılmaz. Kurumsal dağıtımda imzalama ayrıca ele alınabilir.

## Doğrulama ve kanıt

| Kontrol | Sonuç | Kanıt / sınır |
|---|---|---|
| .NET Framework C# derlemesi, 1.1.0 | PASS | Derleyici çıkışı 0 |
| Başlık/yol kodlaması, menü planı, geçersiz yol reddi | PASS | /self-test; Türkçe, boşluk, &, apostrof, yüzde ve cwd uyuşmazlığı |
| Yerel protokol | PASS | Kurulu CLI şeması; 0.150.1 |
| Gerçek boş sohbet oluşturma/adlandırma/açma | PASS | EXE testi: 01a1a01e-0222-78b2-b5bc-d8364c17abc1; doğru başlık/cwd, sıfır tur |
| Prototip sohbetin kullanıcı görsel kabulü | PASS | Kullanıcı “Evet, doğru adla açıldı” |
| 1.0.0 ve 1.0.1 kullanıcı menüsü | FAIL | Kuruldu; klasik menüde görünmedi |
| HKCU düz ve alt menü tanılaması | FAIL görünürlük; PASS temizlik | Açık kullanıcı onayıyla eklendi; ikisi de görünmedi; iki anahtar kaldırıldı |
| Kullanıcı/Explorer yetki farkı | Elendi | Aynı SID, orta bütünlük, elevated=0, AppContainer=0 |
| Explorer önbelleği ihtimali | Sonucu değiştirmedi | Kullanıcı onayıyla mevcut oturumdaki Explorer yeniden başlatıldı; iki pencere geri açıldı; menü görünmedi |
| OneDrive dışındaki klasör | FAIL görünürlük | Yeni work penceresinde de menü yoktu |
| HKLM düz menü tanılaması | PASS | Kullanıcı girişi gördü ve tıkladığında Not Defteri açıldığını bildirdi |
| 1.1.0 kurulum/geçiş ve alt menü görünürlüğü | PASS | Kullanıcı menü/alt seçeneği onayladı; manifest ve beş HKLM kökü doğrulandı |
| Kurulu 1.1.0 menü komutunun eylemi | PASS | Normal yetkide aynı komut doğrudan çalıştırıldı; doğru ad/cwd, boş sohbet; son kabul kaydı aşağıda |
| Gerçek registry hata enjeksiyonu/rollback | NOT EXECUTED | Kaynak incelemesi var; canlı hata enjeksiyonu yapılmadı |
| Gerçek kaldırma | NOT EXECUTED | Kullanıcı kurulumu kalıcı olarak kaldırılmadı |

1.0.1 sonrasında Windows 26H2 build 26300.9818 okundu. Kullanıcı kayıtlarının yüklenmemesinin kesin OS nedeni bilinmiyor. Makine kapsamı farkı kontrollü düz menü testiyle kanıtlandı; kademeli menünün 1.1.0 ile ayrıca kabulü gerekir.

Geçici HKLM testi ChatGPT.Diagnostic.Machine adlı farklı ve tek anahtarı kullanır. Kullanıcı bu test için yönetici kapsamı ekleme/kaldırmaya açık onay verdi. Kaldır ve kapat yalnız test anahtarını temizler. Kalıcı kurulum bu test kaydını yönetmez.

## Testte başarısız olan yollar

İlk app-server proxy denemesi yerel socket hatası verdi; teslimde kullanılmıyor. İlk prototipte UTF-8 stdin sorunu düzeltildi. app-server --stdio yöntemi sonrasında ad/yol doğrulaması geçti.

Windows ekran yardımcısı bazı kurulum/yönetici pencerelerinde “foreground window did not report a process id” hatası verdi. UAC veya bu pencereler otomatik yönetilmedi; kullanıcı tanılama düğmelerini kendisi kullandı. Explorer gözlemi Computer Use ile yapıldı. İlk tanılama penceresinin kullanıcı tarafından yanlışlıkla kapatıldığı belirtildi ve mevcut onayla yeniden açıldı.

## Geri alma ve yedekleme

Normal geri alma: indirilen kurulum EXE'si → Kaldır → kullanıcı yönetici onayı. Yalnız sahipli HKLM kökleri ve mevcut kullanıcıya ait eski paket kökleri kaldırılır. Program Files'taki bilinen EXE/manifest dosyaları temizlenir; dizin yalnız boşsa silinir. Hesaplanmış hedefte recursive dosya silme yapılmaz.

Dışarıdaki kurulum dosyasıyla kaldırma en temiz yöntemdir. Yüklü uygulamalar üzerinden çalışan EXE kendisini silemediğinde EXE ve status=uninstalled sahiplik manifesti korunur; indirilen dış EXE'deki Kaldır ile temizlenir. Önceki 1.1.0/1.1.1 sürümleri manifest bırakmadığı için oluşan bilinen kalıntılar 1.1.2'de yalnız tam SHA-256 eşleşmesiyle tanınır. Sohbetler, çalışma dosyaları ve kullanıcı logları silinmez.

Kurulum yedeği ProgramData altında bu dosyalardan mevcut olanları içerir:

- registry-before.json: HKLM paket anahtarları.
- user-registry-before.json: geçiş öncesi HKCU paket anahtarları.
- ChatGPTMenu-previous.exe ve manifest-before.json: önceki makine sürümü.
- user-ChatGPTMenu-previous.exe ve user-manifest-before.json: eski kullanıcı sürümü.
- icon-before.ico ve icon-target-before.ico: 1.1.1 ve sonrasında varsa önceki/üzerine yazılacak simgeler. Önceki gerçek dosya adı eski manifestte tutulur. Yeni simge yazımı başarısız olursa registry/EXE/manifest ile birlikte simge de geri alınır.

JSON yedekleri .reg değildir. Normal kurulum hatasında bellekteki aynı anlık görüntüler iki hive'a uygulanır ve eski dosyalar geri yazılır. Bu mekanizma canlı hata enjeksiyonuyla test edilmedi. Kesinti/güç kaybına karşı gerçek bir Windows registry transaction'ı yoktur; yedekler ve sahiplik belirteci incelenmelidir.

Önceki makine sürümüne dönmek için mevcut sürümü dış kurulum dosyasıyla kaldırın; yedekteki önceki EXE'yi açıp Kur / Güncelle uygulayın. İlk 1.1.0 geçişinden önceki kullanıcı sürümleri bu bilgisayarda görünmediği için onlara dönüş çalışır menü sonucu sağlamaz. Eski kullanıcı sürümüne bilinçli dönüş yapılacaksa yedekteki user-ChatGPTMenu-previous.exe o sürümün kurulumudur.

Yedekler en az kurulum kabulü ve ilk başarılı kullanım sonuna kadar saklanmalı; otomatik silinmez. Geri alma açılmış Codex sohbetlerini geri almaz. Dosya veri kaybı beklenmez; kayıt ve program temizliği başarısız olursa kullanıcıya hata gösterilir.

## Risk ve etki analizi

Makine menüsü tüm kullanıcılara görünür; EXE ortak ama hesap/oturum/log kullanıcıya özeldir. Başka projeye/API'ye/veritabanına sözleşme etkisi yoktur. Entegrasyon bağı Codex yerel protokolüdür. Arayüz deneyseldir ve sürüm güncellemelerinde yeniden test gerekir.

OneDrive çevrimdışı erişimi, UNC, farklı hesapla UAC ve farklı Windows makineleri test edilmedi. Menü tıklaması işi ayrı EXE'de yürütür; Explorer UI yolunda model çalıştırılmaz. RPC başına 45 saniye sınırı vardır. Çift seçim iki sohbet açabilir.

Kontrol edilenler: iki Explorer bağlamı, tam yol ve UTF-8 akışı, başlık/cwd geri okuma, kullanıcı/makine kayıtları, yetki sınırı, sahiplik, dosya geçişi, yedek ve rollback yolları, kaynak/rehber uyumu. Önceki “klasör seçimi yeni proje oluşturdu” yorumu reddedildi. Program yalnız sohbet oluşturmayı iddia eder. Mevcut sürümde kaynak ve belgeler makine kapsamını birlikte anlatır; tamamlanan kabul ve kalan test sınırları son kabul kaydındadır.

## Sonraki adım

1.1.0 kuruldu; Arnavutköy boş alanındaki alt menü kullanıcı tarafından onaylandı, kurulu eylemin ad/yolu teknik olarak doğrulandı ve geçici kayıt temizlendi. Yeni işlem gerekmez. Kullanıcı isterse sonraki geliştirme ChatGPT Sohbetine Ekle olabilir. Klasör simgesi görünümü ve kaldırma testi ayrıca uygulanabilecek kabul senaryolarıdır.

## Kaynaklar

- [OpenAI: masaüstü bağlantıları](https://learn.chatgpt.com/docs/reference/commands)
- [OpenAI: yerel app-server protokolü](https://developers.openai.com/siwc/token-sharing-open-source/codex-app-server)
- [Microsoft: kademeli menüler](https://learn.microsoft.com/en-us/windows/win32/shell/creating-static-cascading-menus)
- [Microsoft: ExtendedSubCommandsKey](https://learn.microsoft.com/en-us/windows/win32/shell/how-to-create-cascading-menus-with-the-extendedsubcommandskey-registry-entry)
- Makinedeki codex-cli 0.150.1 generate-json-schema çıktısı ve bu sohbetin kullanıcı kabul kayıtları.

## 1.1.0 son kabul ve tutarlılık kaydı

Kullanıcı 1.1.0 kurulumundan sonra **ChatGPT alt menüsünün ve Codex Çalışması Oluştur seçeneğinin göründüğünü** onayladı. Makine manifestinde version=1.1.0, scope=machine doğrulandı. Geçici HKLM Not Defteri test anahtarı kaldırılmıştır; tanılama kayıtları geride bırakılmadı.

Menünün bağladığı kurulu EXE, normal kullanıcı yetkisinde menüdekiyle aynı /open ve Arnavutköy\. argümanlarıyla çalıştırıldı. Çıkış 0; log OPEN_OK. Sohbet kimliği 01a1a047-e867-7e02-b655-a677f50d483b; app read_thread ile başlık **Arnavutköy klasörü**, cwd **C:\Users\Burak Türk\OneDrive\Masaüstü\Arnavutköy**, boş mesaj/tur listesi doğrulandı. Son eylem testi fiziksel menü tıklamasıyla değil, kurulu menü komutunun doğrudan çalıştırılmasıyla yapıldı. Kullanıcı menü görünürlüğünü onayladı; ayrı tanılama menüsünü de tıklayıp Not Defteri açıldığını doğrulamıştı.

Beş HKLM kökü mevcut, aynı beş HKCU kökü yok. Eski AppData EXE ve manifesti kaldırılmış; yedekleri **C:\ProgramData\ChatGPTContextMenu\Backups\20261103-101805-420** altında mevcut. registry-before.json boş makine başlangıcını, user-registry-before.json eski kullanıcı kayıtlarını, user-ChatGPTMenu-previous.exe ve user-manifest-before.json önceki sürümü içeriyor.

Kurulu EXE ile teslim EXE SHA-256 değeri aynı:
~~~text
9A248927655257B140E8D26DA37AB0D4DA6F249D9A3A792F8B2F66C734F98BAB
~~~

Hedef klasör son değişiklik zamanı 2026-08-31 13:50:07 olarak korundu. Bu yalnız klasör meta verisi kontrolüdür, tüm alt dosyaların hash denetimi değildir. Hedef dosyalarda işlem yapılmadı.

**Son kabul durumu:** kurulum/geçiş, kullanıcı tarafından menü görünürlüğü ve kurulu eylemin doğru başlık/klasörle sohbet açması PASS. Klasör simgesindeki ayrı görünüm ve kalıcı programın kaldırılması ayrıca kullanıcı tarafından sınanmadı; kayıt planları mevcut. Gerçek hata enjeksiyonlu rollback, diğer makineler ve yeni Codex sürümleri test edilmedi. Bunlar mevcut Arnavutköy boş alan akışındaki başarılı kabulü genişletmez.

Kaynak kod ve kullanıcı/teknik rehberler makine kapsamı, yönetici gereksinimi, kullanıcı logu ve proje oluşturmama sınırlarıyla uyumludur. Sonraki geliştirme yalnız kullanıcı istediğinde ChatGPT Sohbetine Ekle fazıdır.

## 1.1.1 OpenAI simge güncellemesi

Kullanıcı çalışan menüdeki genel Windows program simgelerinin OpenAI logosu olmasını istedi. Etkilenenler: iki bağlamdaki ChatGPT üst menüsü ve Codex Çalışması Oluştur alt komutu. Sohbet/proje/klasör davranışı ve yerel protokol değiştirilmedi.

Kurulu resmî OpenAI.Codex 26.1027.6604.0 paketindeki chatgpt-app-dark.ico değiştirilmeden source/OpenAI.ico olarak alındı. Kaynak/kopya SHA-256 eşleşti: 7C4BD139C305AC4DE332C12CF4A3B10184715990AFCF48B49F2478FBD1B0E19E. Beyaz logo kullanıcının koyu menüsü için seçildi; açık temada kontrast ayrıca değerlendirilmelidir. Kaynak ayrıntısı ICON-SOURCE.md içindedir.

Build.ps1 simgeyi /win32icon ile EXE'ye ve OpenAI.MenuIcon adıyla yönetilen kaynağa gömer. Kurulum aynı kaynağı Program Files altında OpenAI-1.1.1.ico dosyasına yazar. Dört menü Icon değeri sürümlü dosya yolunu ve 0 indeksini kullanır. Yeni dosya adı eski genel simgenin önbellekte kalmasını azaltır; sistem icon cache'i silinmez ve Explorer otomatik yeniden başlatılmaz.

Makine/kullanıcı kapsamı ve UAC akışı aynıdır. Güncelleme önceki registry/EXE/manifest ile varsa önceki simgeyi yedekler. Manifestte iconFile yalnız doğrulanan OpenAI-sürüm.ico dosya adı olabilir; dış dizine çıkış reddedilir. Geçişte eski simge adı manifestten bulunup yedeklenerek temizlenir. Hata geri alması yeni/önceki simge dosyalarını da geri yükler. Kaldırmada sahipli manifestin simgesi bilinen kurulum dosyalarıyla temizlenir. Canlı rollback hata enjeksiyonu ve ikon kaldırma testi çalıştırılmadı.

Doğrulama: 1.1.1 derleme PASS; paket içindeki ICO'nun 16x16 açılması PASS; dört menu Icon referansı PASS; geçersiz önceki simge dosya adının reddi PASS. Mevcut yol/başlık/UTF-8 kontrolleri de PASS. Windows'un yeni EXE'den çıkardığı simge görsel olarak incelendi ve OpenAI logosu doğrulandı. Yeni model turu veya hedef klasör dosya işlemi yapılmadı. Kaynak, build, manifest, ikon, kullanıcı rehberi ve teknik rehber birlikte güncellendi.

1.1.1 güncellemesi kullanıcı Kur / Güncelle ve UAC onayıyla uygulanır. Bu kayıt yazılırken güncelleme sonrası gerçek menü simgelerinin kullanıcı kabulü beklenmektedir. Önceki 1.1.0'ın çalışan menü/sohbet kabulü geçerlidir; ikon değişikliği kurulumdan sonra ayrıca kontrol edilir. Geri dönüş: mevcut güncellemeyi kaldırıp yedekteki 1.1.0 EXE'siyle yeniden Kur / Güncelle; önceki genel ikonlu görünüme dönülür. Yedekler kabul tamamlanana kadar en az korunmalıdır.

## 1.1.2 sahiplik hatası düzeltmesi ve doğrulama

Kullanıcı simge güncellemesinde dosya sahipliği hatası bildirdi. Salt okunur incelemede Program Files altında yalnız 36.352 baytlık EXE kaldığı, manifest ve menü kayıtlarının bulunmadığı görüldü. Kalan EXE'nin SHA-256 değeri bilinen 1.1.0 teslimiyle birebir aynıydı: 9A248927655257B140E8D26DA37AB0D4DA6F249D9A3A792F8B2F66C734F98BAB. 1.1.1 kurulumunun başarısı iddia edilmez.

Neden: önceki sürüm kendi kurulu EXE'sinden kaldırılırken çalışan dosyayı silemiyor, manifesti siliyordu. Yeniden kurulum da manifestsiz dosyayı bilinmeyen kabul edip reddediyordu. 1.1.2 bu doğrudan ilgili tutarsızlığı düzeltir.

- Manifestsiz kurtarma yalnız iki daha önce üretilmiş EXE'nin tam SHA-256 değeriyle sınırlı: yukarıdaki 1.1.0 ve C419D97A20ED551F35D598A2B2075D7F87A6A649C61FA6A76259E5CD28920607 değerindeki 1.1.1. Genel dosya sahipliği denetimi kaldırılmadı. Bilinmeyen hash ve yabancı registry sahipliği reddedilir.
- Eski dosya önce normal kurulum yedeğine alınır; kurtarma sırasında da aynı rollback akışı kullanılır.
- Yeni kendinden kaldırma EXE ile birlikte owner/scope/version ve status=uninstalled işaretini korur; dışarıdaki kurulumdan kaldırma iki dosyayı da temizler.
- Simge OpenAI-1.1.2.ico adıyla kurulur. Sohbet protokolü veya yetkileri değişmez.

Doğrulama: 1.1.2 build ve yapısal kontroller PASS; bilinen hash kabul/bilinmeyen hash ret kontrolleri PASS; gerçek mevcut manifestsiz 1.1.0 kalıntısında /check-install salt okunur ön kontrol PASS. Bu ön kontrol registry veya kurulum dosyası yazmaz; yalnız çalışma klasörüne test sonucu yazıldı. İkon yükleme ve önceki ikon yolunun dış dizine çıkış reddi kontrolleri PASS.

Kullanıcı yeni paketten Kur / Güncelle ve UAC onayını verdikten sonra sürüm, kurulu ICO hash'i, dört Icon referansı ve iki menü simgesinin görsel kabulü tekrar kontrol edilir. Bu kayıt sırasında 1.1.2 kurulumu/görsel kabulü henüz uygulanmadı. Kendinden kaldırma düzeltmesinin canlı testi ve hata enjeksiyonlu rollback test edilmedi; kaynak düzeyinde incelendi.

### 1.1.2 son kabul

Kullanıcı 1.1.2'nin kurulduğunu ve **iki OpenAI simgesinin doğru göründüğünü** onayladı. Önceki bekleyen kabul kaydı bu son sonuçla kapanmıştır. Makine manifesti version=1.1.2, scope=machine, iconFile=OpenAI-1.1.2.ico olarak doğrulandı. İki bağlamdaki üst ve alt menünün toplam dört Icon kaydı aynı sürümlü tam yola işaret ediyor.

Kurulu ICO'nun SHA-256 değeri resmî kaynağınkiyle eşleşti: 7C4BD139C305AC4DE332C12CF4A3B10184715990AFCF48B49F2478FBD1B0E19E. Kurulu ve teslim EXE'leri de birebir aynı: D54D77FDED0AB0DFA7ADB628CF4CD50E50CC6FC6107D03E5E4DEC144F1C902AB.

Kurtarılan manifestsiz eski EXE, kurulum sırasında normal yedeğe alındı. Yedek konumu: C:\ProgramData\ChatGPTContextMenu\Backups\20261103-104016-561. Ön kontrol ve gerçek kurulum geçişi PASS. Hedef klasör dosyalarında veya Codex sohbet içeriklerinde işlem yapılmadı; ikon değişikliği için yeni sohbet oluşturulmadı. Yeni bağımlılık, API, servis veya yetki politikası değişikliği yoktur.

**Tamamlanan kapsam:** resmî OpenAI simgeleri, sürümlü simge referansları, mevcut sahiplik hatasından güvenli kurtarma, paket/kaynak/rehber uyumu. **Test sınırı:** yeni kendinden kaldırma işaretinin canlı kaldırma testi ve zorlanmış hata geri alma testi uygulanmadı; bunlar yapılmış sayılmaz. Açık tema görünümü bu kullanıcının koyu menü kabulüne dahil değildir.
