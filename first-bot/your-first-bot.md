# Bot Yapmak

Bu bölüm, Başlarken bölümünü ve bot kodunuzun derlediğini varsayar. Ayrıca, tekrarlamak zorundayım: Görmek üzere olduğunuz kodu anlamadıysanız, bir botun kodlanması sizin için olmayabilir. [CodeAcademy] (https://www.codecademy.com/learn/javascript) adresine gidin ve Javascript'i öğrenin. Bu bölümde, bazı yararlı komutlarla basit bir botun geliştirilmesi konusunda size rehberlik edeceğim. İlk bölümde oluşturduğumuz örnekle başlayacağız:

```javascript
const Discord = require("discord.js");
const client = new Discord.Client();
 
client.on("ready", () => {
  console.log("I am ready!");
});
 
client.on("message", (message) => {
  if (message.content.startsWith("merhaba")) {
    message.channel.send("merhaba ben bot");
  }
});
 
client.login("TokenBuraya");
```

## Eventleri Tanıtıyoruz

Before we dive into any further coding, we need to first understand what an _Event_ is.

This is an event:

```javascript
client.on("message", (message) => {
  // This code runs when the event is triggered
});
```

Bu, özellikle, _discord.js_ dosyasındaki bir etkinliktir, ancak diğer API'lerin etkinlikleri nasıl ele aldığına benzer. Bu olay, botun bir mesaj gördüğü her zaman tetikler. Bu, botun eriştiği her kanalı ve aldığı herhangi bir doğrudan veya özel mesajı içerir. Birisi bir kanala 5 mesaj gönderirse, bu olay 5 kez ateşlenir. Bu neden önemli? Botunuzu büyük bir sunucuda kullanmayı düşünüyorsanız veya birden fazla sunucuda olmasını istiyorsanız, bu her an tetikleyen çok sayıda etkinliğe dönüşür. Çok fazla optimizasyon konusuna girmek istemiyorum, ama tek bir nokta için: ** her etkinlik için tek bir olay fonksiyonu kullanın **. Discord.js, belirli durumlarda tetiklenebilecek çok sayıda etkinlik içerir. Örneğin, "hazır" etkinliği, bot çevrimiçi olduğunda tetiklenir. "GuildMemberAdd" etkinliği, yeni bir kullanıcı botla paylaşılan bir sunucuya katıldığında tetiklenir. Etkinliklerin tam listesi için bkz. [Dokümantasyondaki olaylar] (https://discord.js.org/#/docs/main/stable/class/Client?scrollTo=channelCreate). Bu bölümün ilerleyen kısımlarına geri döneceğiz.

## 1 Tane Daha Komut Eklemek

Yapmak isteyebileceğiniz ilk yararlı şeylerden biri, botunuza ikinci bir komut eklemektir.Şimdilik bu yeterli olacaktır.

{% hint style="info" %}
Bundan sonra discord.js'yi gerektiren ve başlatan kodu kaldıracağım ve kodun belirli bölümlerine yoğunlaşacağım.
{% endhint %}

```javascript
client.on("message", (message) => {
  if (message.content.startsWith("sa")) {
    message.channel.send("as");
  } else
 
  if (message.content.startsWith("naber")) {
    message.channel.send("iyi");
  }
});
```

Kodunuzu kaydedin ve botunuzu yeniden yükleyin. Bunu yapmak için , komut satırında `CTRL + C 'kullanın ve' node bot.js 'dosyasını yeniden çalıştırın. Evet, bu kitapta daha sonra göreceğiniz gibi, kodu yeniden yüklemek için daha iyi yollar vardır. Yeni komutunuzu botla paylaştığınız bir kanalda `sa 'diyerek test edebilirsiniz. Ayrıca 'naber' nin hala 'iyi' değerini döndürdüğünü de onaylayabilirsiniz!

## Prefix Kullanarak Komut Yapmak

prefix içeren komutlara çok sayıda botun yanıt verdiğini fark etmiş olabilirsiniz. Bu bir ünlem işareti olabilir \ (! \), Bir nokta \ (. \), Bir soru işareti \ (? \) Veya başka bir karakter olabilir. Bu iki şey için yararlıdır. Öncelikle, benzersiz bir önek kullanmıyorsanız ve bir sunucuda birden fazla botunuz varsa, her ikisi de aynı komutlara cevap verecektir. Geliştirici sunucularında, `! Help` yazarak, kaçınılması gereken bir şey olan cevapların ve özel mesajların taşmasına yol açar. İkincisi, yukarıdaki örnekte, _starts 3_, `foo` ile mesaj verildiğinde yanıt veririz. Şu anki haliyle, bu aşağıdaki cümlenin botun tepkisini tetikleyeceği anlamına gelir: ** aptal, sonuncusu duymadınız! **. Evet, bu garip bir örnek, ama yine de geçerli - bu, botunuzun kanalında söyleyin ve cevap verecektir. Bu konuda çalışmak için bir değişkende saklayacağımız önek kullanıyoruz. Böylelikle önek ve tüm komutlar için tek bir yerde değiştirebilme yeteneği elde ederiz. İşte bunu yapan bir örnek kod:

```javascript
// Set the prefix
const prefix = "!";
client.on("message", (message) => {
  // Exit and stop if it's not there
  if (!message.content.startsWith(prefix)) return;
 
  if (message.content.startsWith(prefix + "ping")) {
    message.channel.send("pong!");
  } else
  if (message.content.startsWith(prefix + "sa")) {
    message.channel.send("as");
  }
});
```

The changes to the code are still simple. Let's go through them:

* `const prefix = "!";` burdan prefix ayarlayabilirsiniz.
* The line `if(!msg.content.startsWith(prefix)) return;` küçük bir optimizasyon bitiştir: "Eğer mesaj önekimle başlamıyorsa, ne yaptığınızı durdurun". Bu, işlevin geri kalanının çalışmasını engelleyerek botunuzu daha hızlı ve daha duyarlı hale getirir.
* Komutlar değişti, bu nedenle bu öneki `startsWith(prefix + "ping")` komut kullanıldığında bot mesajı cevaplar `!ping`.

The second point is just as important as having a single `message` event handler. Let's say the bot receives a hundred messages every minute \(not much of an exaggeration on popular bots\). If the function does not break off at the beginning, you are processing these hundred messages in each of your command conditions. If, on the other hand, you break off when the prefix is not present, you are saving all these processor cycles for better things. If commands are 1% of your messages, you are saving 99% processing power...

{% hint style="info" %}
OK I'm sorry, I'm bullshitting a little. It's not 99%, that is an exaggeration. It _is_, however, true that you save a ton on processor and RAM power.
{% endhint %}

## Preventing Botception

We're pretty much done with the basic bot. There's one last thing that I want to talk about: bots answering each other. Let's pretend for a moment that you have two bots on your server and each can respond to the same prefixed command, `!help`. But when that command is called, it replies: `!help commands: Type !help followed by one of the following to see details: ping , foo`.

Now, one person types `!help` in a channel, and both bots respond. But, they will also see the **other** bot saying `!help commands: [...]`, will see that as a request for help, answer each other... in an infinite loop. To prevent that from happening, we can add a second condition inside our `message` event handler, right below the one that checks for the prefix:

```javascript
const prefix = "!";
client.on("message", (message) => {
  // our new check:
  if (!message.content.startsWith(prefix) || message.author.bot) return;
  // [rest of the code]
});
```

That condition contains an _OR_ \( \|\| \) operator, which reads as the following:

{% hint style="info" %}
If there is no prefix or the author of this message is a bot, stop processing. This includes this bot, itself.
{% endhint %}

And now, we have a bot that only responds to 2 commands and does not waste any power trying to figure out anything else. Is this a complete basic bot? Sure! So let's end this page here and we'll take a look at some new concept next.

The full bot code would now be:

```javascript
const Discord = require("discord.js");
const client = new Discord.Client();
 
// Set the prefix
let prefix = "!";
client.on("message", (message) => {
  // Exit and stop if the prefix is not there or if user is a bot
  if (!message.content.startsWith(prefix) || message.author.bot) return;
 
  if (message.content.startsWith(prefix + "ping")) {
    message.channel.send("pong!");
  } else
  if (message.content.startsWith(prefix + "foo")) {
    message.channel.send("bar!");
  }
});
 
client.login("SuperSecretBotTokenHere");
```

Every time I see that `SuperSecretBotTokenHere`, I cringe a little. See, it's not good practice to have tokens and auth stuff in your code, it really should be in a separate file! Head on over to [Adding a Config File](adding-a-config-file.md) and let's get this done.

