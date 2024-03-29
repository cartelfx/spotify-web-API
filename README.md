# Spotify WebSocket Etkinlik API Kütüphanesi

## Bu API nasıl çalışır?

- **Spotify Web API**, Spotify ile bağlantı kurmak için sizin için bağlanan bir sınıftır.
1. Spotify kimlik doğrulamanızı alır ve Spotify ile bağlantı kurmak için bir token oluşturur.
2. Daha sonra, WebSocket üzerinden bildirimler gönderen birden fazla Spotify "bayi"den birine bağlanır.
3. Bu soketi **SpotifyClient**'a iletir, ancak Spotify ile kendi başınıza da bağlanabilir ve onu **SpotifyClient**'a iletirsiniz.

- **SpotifyClient**, WebSocket'e bağlandıktan sonra her şeyi işler, bu hala bazı gereken REST isteklerini içerebilir.
1. Spotify'dan bir başlangıç yükleme verisi almayı bekler, bu bir bağlantı kimliği içerir.
2. `PUT https://api.spotify.com/v1/me/notifications/user?connection_id=${connectionID}` adresini arar ve bağlantı kimliği ile ilişkilendirilen hesaptaki etkinliklere abone olur ve dolayısıyla sağladığınız yetkilendirme.
3. `POST https://guc-spclient.spotify.com/track-playback/v1/devices` adresini arar ve Spotify'dan bildirim alacak **sahte** bir Spotify Web İstemcisi geçici olarak kaydeder.
4. `PUT https://guc-spclient.spotify.com/connect-state/v1/devices/hobs_${clientID}` adresini arar ve ortam oynatıcı etkinliklerine abone olur.
5. **SpotifyClient**, ortamın varlığı değiştikçe çeşitli olaylar yayar.

| Olay Adı   | Açıklama                                                                                          | Veri Türü       |
|------------|--------------------------------------------------------------------------------------------------|-----------------|
| volume     | Ses düzeyi her değiştiğinde yayınlanır.                                                         | sayı            |
| playing    | Müzik tekrar çalındığında yayınlanır.                                                           | yok             |
| stopped    | Müzik durdurulduğunda yayınlanır.                                                                | yok             |
| paused     | Müzik duraklatıldığında yayınlanır.                                                              | yok             |
| resumed    | Müzik devam ettirildiğinde yayınlanır.                                                           | yok             |
| track      | Yeni bir parça çalındığında yayınlanır.                                                         | SpotifyParça    |
| options    | Oynatma seçenekleri değiştiğinde (karışık, tekrar, tek tekrar) yayınlanır.                       | OynatmaSeçenekleri |
| position   | Bir şarkıdaki konum değiştiğinde yayınlanır. Bu, yeni bir parçanın başında da içerir.            | dize            |
| device     | Müziği çalan cihaz değiştiğinde yayınlanır.                                                      | SpotifyCihazı   |
| close      | WebSocket kapatıldığında yayınlanır. Yeniden bağlanmanın belirli bir süre sonra gerçekleşmesi gerektiğini gösterir. | yok             |


## Başlamadan önce
Sactivity, Spotify tarafından giriş yapıldıktan sonra verilen çerezlerle çalışır ve oldukça uzun süre korunur gibi görünür. İhtiyaç duyulan çerezleri nasıl alacağınız aşağıda açıklanmıştır:
1. Chrome'u açın.
2. Geliştirici araçlarını açın.
3. Ağ denetleyicisine gidin ve filtre kısmına "get_access_token" yazın.
4. Bu sekmede https://open.spotify.com adresine gidin.
5. Oluşan ağ isteğine tıklayın ve Ardışık başlıklar içindeki `cookie` bölümünü tamamını kopyalayın.
