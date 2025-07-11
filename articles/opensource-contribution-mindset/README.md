# 貢獻開源專案應有的心態

這邊蒐集了散落在各處的社群成員關於貢獻開源專案應有的心態的看法，包含 Slack 的訊息或是 FB 的貼文等，內容若提到個人的資訊會被改寫掉。

## 給想要成為Kafka committer的伙伴們

- 作者：Luke
- 背景：最近看到嘉平老大在private channel賣臉propose我們的人成為committer，但沒有收到任何回應，我覺得我應該雞婆點來幫忙說幾句話。嘉平老大是那種會默默幫忙大家的人，就像他說的，幫大家搭舞台，但我自己是覺得，除了靠嘉平幫忙，自己也要多付出一些努力才行。

先說一下，要成為committer，必須至少要7位PMC member +1才行，大家可以想想，自己如果被提名了，有把握拿到幾票？假設我和嘉平都+1了，還有其他5人要認可你才行。所以有誰呢？

我來分享我當時的經驗給你們聽，畢竟我自己知道我不是嘉平大那種神人，可以短時間內自己找到很多好題目做，而且可以做到Confluent的大老來請他去Confluent上班，所以我就是整整花他2倍的時間才成為committer的人（我是2年拿到!)，當然更別說我那時候沒有什麼mentor找題目，手把手教學，幫忙review/merge PR的，全部靠自己，所以那時候在貢獻一年多時，我看到有人成為committer，然後再看他/她的achievement，心裡就會想，靠！憑什麼他這樣可以成為committer，我不行，明明我也xxxooo！！！！雖然心裡這樣想著，還是不甘心的回信寫"Congrats, well deserved"這種昧著良心的話！BTW，下禮拜會有一個新的committer announce，不是我們這裡的人，所以如果有人有跟我當時一樣的心情，相信我，很正常！(哭)

回過來分享一下我的心得，我當時的想法是，好，我要讓多一點PMC member認識我，看到我的好，去拿到+1，所以我幾乎每天會去看新開的PR，還有前一天merge的PR，去看現在哪些PMC對哪些topic很重視（所以其實只看PMC開的review的PR XD)，然後就再鑽進去看更深，就算merge進去的PR，我還會再看一次，也會去看KIP，還有他們的implementaion，去想有什麼地方是他們漏掉的，去抓出來讓他們impressive！講得很抽象，來舉幾個例子：
1. KAFKA-13008: 我是發現在某個功能進去後，有個test偶爾會flaky，花了幾個禮拜去追，挖到這個bug！從comment可以看到我可以至少拿到3張PMC的+1
2. KAFKA-13598: 在kafka v3.0.0 release時，有一個很大的改變，就是idempotent producer is enabled by default. 這被promote很多，因為可以解決長年的duplicate record的問題，但被我發現v3.0.0其實沒有enable idempotent by default，根本就在騙肖仔XD，從這個 PR comment from Ismael 可以看到Ismael老大也很驚訝:

Are you saying that the change to enable idempotence by default in 3.0 was broken? And we shipped 3.1 with the same critical bug?

3. KAFKA-13033: 講完這個就好，不然搞得好像我要promote自己成為committer一樣XD 這個是現在的kafka leader, Mickael在做完KIP-699然後merge之後，我去重新review他的PR發現的bug，所以可以想見，這個讓Mickael(當時不認識我），和當時review他PR的David Jacot印象分數++

反正我想說的是，我想成為committer，所以去了解遊戲規則，想辦法幫自己拿到更多+1，這就是我當時的方法，跟你們分享！一起加油！

## 開源貢獻流程

- 作者：陳楷訓
- 背景：有新手說想要在暑假的 75 天內挑戰成為 Apache 其中一個專案的 Committer。

我自己的經驗 75 天成為 committer 在多數專案是不太可能的，通常搞開源流程是這樣：

(1) 先解決一些只有一種解法的問題 ex: 修 doc / 簡單 bug / 加 test，並且質量要很高，社群 maintainer 才會願意幫你 review，基本上一個新的 contributor 的 PR review 花的時間，通常 maintainer 可以做好幾遍，並且質量更好。就算是大科技公司的工程師開的 PR 也是如此。

(2) 你在 (1) 有一定累積以後，開始找一些簡單但有超過一種解法的問題，然後開 issue 說明自己對各種解法的看法，並跟 maintainer 討論，again 基本上 maintainer 自己做會遠比討論有效率，因此寫的 issue 要有料、並且覺得你有潛力，maintainer 才比較有機會願意跟你討論。

(3) 當你 (2) 有一定累積，接下來可能可以自己獨立判斷 (2) 這種簡單問題，哪種方案比較好，這樣你解決簡單問題週期會變快，有些情況如果該問題有一個答案遠比其他方案好，甚至可以直接開 PR。

(4) 你在 (2)(3) 的過程中，會累積一些對於這個專案的 context，同時可以去 review 一些 PR / 回答社群的問題，同樣必須是高質量的 review，maintainer 大概可以知道你有沒有料。

(5) 等你 (4) 做到很好，有某方面相關問題 / PR，社群第一個會想到你的時候，maintainer 可能會開始把一些沒這麼緊迫，但比較開放性的問題交給你。這些問題同樣規模不會很大。

(6) 當你 (5) 做到一定程度，你完全和社群 build trust，才會將比較重要的 feature 給你做。

通常 (6) 累積一定才算是在社群有一席之地，可能成為 committer，有些專案可能會希望你 initiate 一個新的 module 才算有足夠 impact。

我個人 mentor 過 20+ 人做開源，真的自己走 (1) ~ (6) 的只有一位，其他成為 committer 有一部分原因是我和其他 mentors，強行幫這些 mentee 真氣灌頂，直接跳過 (1)(2)(3)(4)，直接開始 (5)(6)，而這些 (5) 又是我們 well-defined 好的東西。

我覺得大三開始接觸開源已經是很好的開始，暑假可以先把目標訂在把 (1)(2) 做好，你時間還很多，可以慢慢往 (3)(4)(5)(6) 靠近。

## 我要先學完 XXXX，才能開始 YYYY？

- 作者：陳楷訓
- 背景：有一位學生說他要把 XXX 課程學完之後再來開始貢獻開源專案

分享一些我的小經驗分享，我發現通常「我要看完 XXXX，才能開始 YYYY」，這種 mindset 通常在學生時期修課 / 準備考試比較有用，對於做研究 / 開源 / 工作，這會讓效率降低很多，研究 / 開源 / 工作，建議：

(1) 容忍未知事物 (black box)，並且使用它

(2) 找到你可以理解 / 並且你可以立刻做出貢獻的地方

像是我大學做研究時，有些研究生跟我一起做研究，很常有人說，自己看得 paper 不夠多，不能開始做研究，然後就看了一年，碩二還是不知道要幹啥，我大學生論文都發完了。

開源也是，我自己做開源是：

(1) Submarine 時期：先學 XX，想辦法找地方給他用上 (ex: 看了 k8s 課程一小部分，找哪裡可以用上)

(2) 現在：現在經驗比較多，skillset 也相對全，就算是看似我沒有 context knowledge 的專案，我也可以很快找到我已經有 skillset 我可以理解的地方開始貢獻，像是我對 inference 內部不太理解，但是我去玩 SGLang 的時候還是可以順手水幾個 PRs，因為我可以找到我會的東西。

總之，學習盡量要目的導向 ex: 我要解決什麼問題，而解決這些問題 XX 是必要知識，所以我去學 XX

## 想學習一個library或其他CS相關知識時，你們會直接看documentation嗎?

- 作者：鄭黃翔
- 背景：有人提文說「聽聞很多人都建議看documentation，但自己讀起來覺得太細而且太耗時間」
1. 如果一個 library scope 很限定，譬如說 GeoHash， 那麽我大概會先看看他的單元測試 (unit tests) 的範例，快速了解一下這個 library 在幹嘛。通常看完也差不多懂了
2. 如果一個 library scope 比較大，譬如說 [Functional Java](https://github.com/functionaljava/functionaljava)，那個除了單元測試以外，搭配文件是必要的，這樣才能比較徹底了解用處。然而，就跟學英文一樣。我們比較不會把字典背起來，才來想怎麼說英語。可以反過來操作，遇到應用場景，再去深入了解。
3. 我以一個特定的應用舉例: 資料庫。理論部分可以看 YouTube 教材/教科書。但實際上應用會選定一個特定的資料庫，譬如說 PostgreSQL。然而一個成熟的資料庫，例如 PostgreSQL，功能實在太多了，我一輩子也看不完。所以跟嘉平說的一樣，先找一個實際上用得到的案例開始，然後試試看文件上的範例。真的有需要進入細節，再去看更深入的材料就好。
4. 當然也有很多江湖傳言，說某某駭客從小就看 Linux manual 長大，我是覺得不用這樣啦。特別是 T 大資工系經常有很多傳說，這些傳說不是非常適合剛剛加入新手村的朋友放在心上 
