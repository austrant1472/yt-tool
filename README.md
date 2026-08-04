# yt-tool
yt-dbl yi daha basit stabil ve optimize hale getirmek için bir yt-dbl aracısı

kurulum

    yt-tool dosyasını ~/bin/ içine at

bağımlılıklar
deno/bun (bilgisayar için önerilen js runtime)
nodejs (termux için önerilen js runtime)
ffmpeg
ffplay
yt-dlb
(script deno nodejs veya bun dan hangisi sistemde kuruluysa otomatik olarak onu kullanır)

optimizasyon ve stabilite algoritmaları

    önbellek karar matrisi (Smart Cache): video oynatılmadan önce parametrelerde aksi belirtilmedikçe varsayılan olarak video boyutu sorgulanır eğer video 500 MB'nin altındaysa doğrudan RAM'e (/dev/shm) indirilir oynatılır ve bitince RAM'den otomatik silinir eğer video 500 MB'nin üstündeyse diski yormamak için dinamik akış (stream/pipe) moduna geçilir

    gerçek zamanlı valf senkronizasyonu: pipe akışında internet hızı video izleme hızını geçerse ffplay videoyu hızlı oynatır script araya ffmpeg -re valfini koyarak verinin tam 1x (gerçek zamanlı) hızda akmasını sağlar ses ve görüntü senkronizasyonunu korur

    dil tabanlı altyazı tahmini: video indirilirken dili kontrol edilir eğer video türkçe ise altyazı indirilmez türkçe değilse sessizce İngilizce altyazı çekilip videoya gömülür

    donanım odaklı codec seçimi: işlemciyi donanımsal olarak yormamak için youTube dan AV1 yerine her zaman H.264 (avc1) codec türü talep edilir bu sayede oynatma sırasında donma ve yüksek işlemci kullanımı önlenir

    sürümlü dosya çakışma yönetimi: yt-dlp aynı videoyu farklı kalitelerde indirdiğinde ya üstüne yazar ya da hata verir yt-tool indirmeden önce klasörü tarar eğer aynı video ID'sine sahip bir dosya varsa onu silmeden adının sonuna _vESKI_KALITE ekler ve yeni dosyayı _v720p_avc1 gibi kalite bilgisiyle kaydeder böylece aynı videonun 720p ve 1440p versiyonları yan yana durabilir

    dinamik PATH ve runtime enjeksiyonu: yt-dlp, deno veya nodejs kurulsa bile bazen terminalin PATH ayarlarından dolayı onları bulamaz script her çalıştığında sistemdeki olası tüm gizli yolları ($HOME/.deno/bin, $HOME/.nvm/current/bin vb.) tarar JS runtime ı bulursa kendi oturumu için geçici olarak PATH değişkenine ekler ve --js-runtimes parametresiyle yt-dlp'ye tanıtır kullanıcı hiçbir ekstra ayar yapmadan PO Token sorunu çözülür

    platforma özel istemci manuplasyonu (termux/android): termux üzerinde yt-dlp çalıştığında youtube android vr API ını kullanır ve çoğu zaman kilitlenir script termux ortamını algılar ve youtube a --extractor-args "youtube:player_client=tv,android" paketini göndererek "VR değil bir android TV taklidi yapar" bu sayede youtube un en az kısıtlamalı (PO Token gerektirmeyen) API ını kullanılarak indirme tamamlanır

 kullanım ve parametreler

script kullanım kolaylığı için parametreleri kategorilere ayırır istediğiniz parametreleri birleştirerek kullanabilirsiniz
 video indirme

    -sponsor       : sponsorblock'u aktifleştirir videodaki sponsor intro outro gibi kısımları keser
    -transt        : otomatik oluşturulmuş altyazıyı indirir (tr,en)
    -low           : 720p 30fps'e düşürür ve hızlı sıkıştırma (CRF 26) uygular
    -sound-m4a     : sadece sesi çeker ve M4A (AAC 96k) formatına dönüştürür
    -sound-opus    : sadece sesi çeker ve opus (64k VBR) formatında kaydeder

Anlık Oynatma (Oynatıcı: ffplay)

    -play          : videoyu indirmeden anlık olarak oynatır
    -loop          : videoyu sürekli döngüde (tekrar tekrar) oynatır
    -sound         : sadece sesi oynatır (Video penceresi açılmaz, arka planda çalar)
    -video         : sadece görüntüyü oynatır (Ses gelmez)
    -vol <0-100>   : oynatma modunda başlangıç ses seviyesini belirler (Örn: -vol 70)

Oynatma Önbellek (Cache) Modları (-play ile kullanılır)

    -cache-ram     : Boyutu umursamadan videonun tamamını RAM'e indirir sonra oynatır
    -cache-ssd     : Videonun tamamını geçici olarak SSD'ye (/tmp) indirir oynatır ve siler
    -cache-stream  : Boyutu umursamadan dinamik akış (pipe) moduna zorlar

Arama ve Kanal Keşfi

    -search        : youtube'da video arar ve ilk 10 sonucu listeler
    -csearch       : youtube'da kanal arar ve ilk 10 kanalı listeler
    -chvid         : belirtilen kanalın son 30 videosunu listeler
    -chinfo        : belirtilen kanalın bilgisini (abone sayısı, video sayısı vb.) gösterir

Geçmiş (History) Görüntüleme

    -history-download : indirme geçmişini yazar
    -history-play     : oynatma geçmişini yazar
    -history-search   : arama geçmişini yazar

debug

    -verbose       : detaylı log verir

kılavuz

    -h, --help   : Yardım menüsünü gösterir

Yasal Uyarı

Bu araç ilk çalıştığında kullanıcıdan yasal uyarıyı kabul etmesini ister ve bu onay ~/.local/share/yt-toolkit/disclaimer_accepted dosyasına kaydedilir. Bu araç eğitim ve kişisel kullanım amaçlıdır. YouTube Hizmet Şartlarını ihlal etmekten veya telif hakkı ihlali yapmaktan tamamen KULLANICI sorumludur. Geliştirici hiçbir yasal sorumluluk kabul etmez.
