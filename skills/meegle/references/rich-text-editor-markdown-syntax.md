# [translated text] [translated text] [translated text] Markdown [translated text] [translated text]

## [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] **GFM（GitHub Flavored Markdown）** [translated text] [translated text] Markdown [translated text] 。 [translated text] [translated text] Markdown [translated text] [translated text] [translated text] [translated text] ， [translated text] HTML [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] DSL、Delta [translated text] doc_html。

** [translated text] [translated text] ：** [translated text] GFM + HTML [translated text] [translated text] [translated text] [translated text] + `<span>`/`<u>` [translated text] [translated text] [translated text] [translated text] 。

## [translated text] [translated text]

| [translated text] | [translated text] | [translated text] |
|------|------|------|
| [translated text] | `** [translated text] **` | |
| [translated text] | `* [translated text] *` | |
| [translated text] [translated text] | `~~ [translated text] ~~` | |
| [translated text] [translated text] | `<u> [translated text] </u>` | HTML [translated text] ， [translated text] [translated text] MD |
| [translated text] [translated text] | `` ` [translated text] ` `` | |
| [translated text] [translated text] | `<span style="color: rgb(R, G, B)"> [translated text] </span>` | [translated text] [translated text] `rgb()` [translated text] |
| [translated text] [translated text] | `<span style="background-color: rgb(R, G, B)"> [translated text] </span>` | [translated text] [translated text] `rgb()` [translated text] |
| [translated text] [translated text] | `<span style="font-size: Npx"> [translated text] </span>` | [translated text] px [translated text] |
| [translated text] | `#` [translated text] `######` | h1-h6 |
| [translated text] [translated text] | `1. [translated text] ` | [translated text] [translated text] 4 [translated text] [translated text] |
| [translated text] [translated text] | `- [translated text] ` | [translated text] [translated text] 4 [translated text] [translated text] |
| [translated text] [translated text] | `- [ ] [translated text] ` / `- [x] [translated text] [translated text] ` | |
| [translated text] [translated text] | `> [translated text] ` | [translated text] [translated text] [translated text] [translated text] [translated text] |
| [translated text] [translated text] | ` ``` [translated text] ... ``` ` | [translated text] [translated text] [translated text] [translated text] [translated text] |
| [translated text] | `[ [translated text] ](url)` | |
| [translated text] | `![ [translated text] ](url)` | [translated text] [translated text] [translated text] [translated text] [translated text] UUID， [translated text] [translated text] [translated text] [translated text] |
| [translated text] [translated text] | `[ [translated text] ](url)<!-- linkPreview -->` | [translated text] [translated text] [translated text] |
| [translated text] [translated text] | `---` | |
| [translated text] | `:ShortCode:` | [translated text] [translated text] [translated text] [translated text] [translated text] |
| [translated text] [translated text] | `<!-- center:start -->` ... `<!-- center:end -->` | [translated text] [translated text] |
| [translated text] [translated text] | `<!-- right:start -->` ... `<!-- right:end -->` | [translated text] [translated text] |
| [translated text] [translated text] | `<!-- justify:start -->` ... `<!-- justify:end -->` | [translated text] [translated text] |
| @ [translated text] | `@ [translated text] <!-- mention:{JSON} -->` | [translated text] [translated text] [translated text] |

## [translated text] [translated text] [translated text]

### [translated text] [translated text] （ [translated text] [translated text] ）

 [translated text] start/end [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] ：

```markdown
<!-- center:start -->
 [translated text] [translated text] [translated text] [translated text] 。

 [translated text] [translated text] [translated text] [translated text] 。
<!-- center:end -->

<!-- right:start -->
 [translated text] [translated text] [translated text] 。
<!-- right:end -->
```

 [translated text] [translated text] ：`center`（ [translated text] ）、`right`（ [translated text] [translated text] ）、`justify`（ [translated text] [translated text] ）。 [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] 。

### @ [translated text] （ [translated text] [translated text] ）

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 。`@ [translated text] ` [translated text] [translated text] [translated text] ** [translated text] [translated text] [translated text] **：

```markdown
@ [translated text] <!-- mention:{"id":"lark_user_id_7361251974161006596","cn_name":" [translated text] ","en_name":"Zhang San","email":"zhangsan@example.com","blockType":"AT_USER_BLOCK"} -->
```

 [translated text] [translated text] [translated text] JSON [translated text] ：

| [translated text] | [translated text] | [translated text] |
|------|------|------|
| `id` | [translated text] ID | `"lark_user_id_7361251974161006596"` |
| `cn_name` | [translated text] [translated text] | `" [translated text] "` |
| `en_name` | [translated text] [translated text] | `"Zhang San"` |
| `email` | [translated text] [translated text] | `"zhangsan@example.com"` |
| `blockType` | [translated text] [translated text] | `"AT_USER_BLOCK"` |

 [translated text] [translated text] ：`blockId`（UUID v4）、`type`（0 = [translated text] ）、`avatar_url`。

 [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] ）：

```markdown
@ [translated text] <!-- mention:{"id":"id_1","cn_name":" [translated text] ","en_name":"Zhang San","email":"zhangsan@example.com","blockType":"AT_USER_BLOCK"} -->@ [translated text] <!-- mention:{"id":"id_2","cn_name":" [translated text] ","en_name":"Li Si","email":"lisi@example.com","blockType":"AT_USER_BLOCK"} -->
```

 [translated text] [translated text] [translated text] [translated text] ， [translated text] `@ [translated text] ` [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

### [translated text]
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Markdown [translated text] [translated text] ， [translated text] [translated text] [translated text] UUID：

```markdown
![ [translated text] ](https://example.com/page/***)
```

 [translated text] UUID [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] UUID [translated text] ， [translated text] ：

```markdown
![ [translated text] ](https://example.com/page/***)<!-- *****-*****-**** -->
```

 [translated text] UUID [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

### [translated text] [translated text]

 [translated text] [translated text] [translated text] `<!-- linkPreview -->`（** [translated text] [translated text] **）：

```markdown
[https://example.com/page](https://example.com/page)<!-- linkPreview -->
```

### [translated text] [translated text]

 [translated text] Markdown [translated text] [translated text] [translated text] ， [translated text] HTML `<u>` [translated text] ：

```markdown
<u> [translated text] [translated text] [translated text] [translated text] </u>
```

 [translated text] [translated text] [translated text] [translated text] ：

```markdown
*<u> [translated text] [translated text] [translated text] </u>*
**<u> [translated text] [translated text] [translated text] </u>**
```

### Span [translated text]

 [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] [translated text] `<span>` [translated text] `style` [translated text] 。** [translated text] [translated text] [translated text] `rgb(R, G, B)` [translated text] **（ [translated text] [translated text] hex [translated text] [translated text] ）：

```markdown
<span style="color: rgb(245, 74, 69)"> [translated text] [translated text] </span>
<span style="background-color: rgb(53, 189, 75)"> [translated text] [translated text] </span>
<span style="font-size: 18px"> [translated text] [translated text] </span>
```

### [translated text] [translated text] [translated text]

 [translated text] `:CODE:` [translated text] 。 [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] lark [translated text] [translated text] ：

```
:OK: :DarkThumbsup: :THANKS: :DarkFightOn: :DarkFingerHeart: :APPLAUSE: :LightFistBump: :JIAYI: :DONE: :SMILE: :Delighted: :BeamingFace: :BLUSH: :LAUGH: :SMIRK: :LOL: :FACEPALM: :LOVE: :ERROR: :CRY: :SOB: :THINKING: :SCOWL: :SMART: :WITTY: :PROUD: :WINK: :NOSEPICK: :HAUGHTY: :SLAP: :SPITBLOOD: :TOASTED: :ColdSweat: :BLACKFACE: :FullMoonFace: :GLANCE: :DULL: :ROSE: :HEART: :PARTY: :INNOCENTSMILE: :SHY: :CHUCKLE: :JOYFUL: :WOW: :OBSESSED: :DROOL: :SMOOCH: :KISS: :EMBARRASSED: :TEARS: :ENOUGH: :YEAH: :TRICK: :MONEY: :TEASE: :SHOWOFF: :COMFORT: :CLAP: :PRAISE: :STRIVE: :XBLUSH: :SILENT: :HUG: :WHIMPER: :CRAZY: :WAIL: :LOOKDOWN: :DIZZY: :FROWN: :WHAT: :WAVE: :BLUBBER: :WRONGED: :HUSKY: :SHHH: :SMUG: :ANGRY: :HAMMER: :SHOCKED: :TERROR: :PUKE: :SICK: :YAWN: :DROWSY: :SLEEP: :SPEECHLESS: :SWEAT: :SKULL: :PETRIFIED: :BETRAYED: :HEADSET: :EatingFood: :Typing: :Lemon: :Get: :LGTM: :OnIt: :OneSecond: :YouAreTheBest: :Shrug: :ThanksFace: :SaluteFace: :GoGoGo: :Partying: :VRHeadset: :MeMeMe: :Sigh: :DarkSalute: :DarkShake: :LightHighFive: :DarkWavingHand: :DarkClick: :DarkThumbsDown: :ClownFace: :SLIGHT: :TONGUE: :LIPS: :SiSiASYouWish: :HappyDragon: :JubilantRabbit: :RoarForYou: :CALF: :BULL: :BEAR: :EYESCLOSED: :BEER: :CAKE: :GIFT: :CUCUMBER: :Drumstick: :Pepper: :CANDIEDHAWS: :BubbleTea: :Coffee: :Pin: :AWESOMEN: :Hundred: :MinusOne: :CrossMark: :CheckMark: :OKR: :No: :Yes: :Alarm: :Loudspeaker: :Trophy: :Fire: :RAINBOWPUKE: :Music: :TV: :Movie: :Pumpkin: :LUCK: :FORTUNE: :REDPACKET: :BeAtTheForefront: :2026: :FIREWORKS: :XmasHat: :Snowman: :XmasTree: :FIRECRACKER: :StickyRiceBalls: :Mooncake: :MoonRabbit: :HEARTBROKEN: :BOMB: :POOP: :18X: :CLEAVER: :GeneralWorkFromHome: :GeneralBusinessTrip: :StatusFlashOfInspiration: :StatusReading: :GeneralInMeetingBusy: :Status_PrivateMessage: :GeneralDoNotDisturb: :Basketball: :Soccer: :StatusEnjoyLife: :GeneralTravellingCar: :StatusBus: :StatusInFlight: :GeneralSun: :GeneralMoonRest: 
```

 [translated text] [translated text] [translated text] [translated text] [translated text] （`:smile:` → `:SMILE:`，`:beamingface:` → `:BeamingFace:`）， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

### [translated text] — 4 [translated text] [translated text]

 [translated text] [translated text] [translated text] **4 [translated text] [translated text] **（ [translated text] 2 [translated text] ） [translated text] ：

```markdown
1. [translated text] [translated text] [translated text]
 1. [translated text] [translated text] （4 [translated text] ）
 1. [translated text] [translated text] （8 [translated text] ）
 - [translated text] ： [translated text] [translated text] [translated text] [translated text] （4 [translated text] ）
- [translated text] [translated text] [translated text]
 - [translated text] [translated text]
 - [translated text] [translated text]
 1. [translated text] ： [translated text] [translated text] [translated text] [translated text]
```

 [translated text] [translated text] ：

```markdown
- [ ] [translated text] [translated text] [translated text]
- [x] [translated text] [translated text] [translated text]
 - [ ] [translated text] [translated text] [translated text]
 - [x] [translated text] [translated text] [translated text]
```

### [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

```markdown
> [translated text] ** [translated text] ** [translated text] * [translated text] * [translated text] [translated text]
> 1. [translated text] [translated text] [translated text] [translated text]
> 2. [translated text] [translated text]
> 1. [translated text] [translated text] [translated text] [translated text]
> - [translated text] [translated text] [translated text] [translated text]
```

### [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] ：

````markdown
```TypeScript
function add(a: number, b: number): number {
  return a + b;
}
```

```Go
func add(a, b int) int {
    return a + b
}
```
````

### GFM [translated text] （ [translated text] [translated text] ）

 [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] 、 [translated text] 、 [translated text] [translated text] ） [translated text] [translated text] ：

```markdown
| [translated text] 1 | [translated text] 2 | [translated text] 3 |
|-------|-------|-------|
| [translated text] [translated text] 1 | ** [translated text] ** | [ [translated text] ](url) |
| [translated text] [translated text] 3 | [translated text] [translated text] 4 | [translated text] [translated text] 5 |
```

### HTML [translated text] （ [translated text] [translated text] [translated text] [translated text] ）

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] 、 [translated text] 、 [translated text] [translated text] 、 [translated text] 、 [translated text] ） [translated text] ， [translated text] HTML `<table>` [translated text] 。**`<td>` [translated text] `</td>` [translated text] [translated text] [translated text] **， [translated text] [translated text] [translated text] Markdown [translated text] [translated text] [translated text] [translated text] ：

```markdown
<table>
<tr>
<td>

# [translated text] [translated text] [translated text]

** [translated text] [translated text] **

</td>
<td>

1. [translated text] [translated text]
2. [translated text] [translated text] [translated text]
 - [translated text] [translated text]

</td>
<td>

<!-- center:start -->
 [translated text] [translated text] [translated text]
<!-- center:end -->

</td>
</tr>
<tr>
<td>

```TypeScript
// [translated text] [translated text] [translated text] [translated text]
const x = 1;
```

</td>
<td>

> [translated text] [translated text] [translated text] [translated text]

</td>
<td>

<span style="color: rgb(245, 74, 69)"> [translated text] [translated text] [translated text] [translated text] </span>

</td>
</tr>
</table>
```

 [translated text] [translated text] ：`<td></td>`

## [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] ：

```markdown
**~~ [translated text] [translated text] [translated text] ~~**
*<u> [translated text] [translated text] [translated text] </u>*
[** [translated text] [translated text] **](https://example.com)
<span style="color: rgb(245, 74, 69)">** [translated text] [translated text] **</span>
```

## [translated text] [translated text]

 [translated text] [translated text] [translated text] （ [translated text] 、 [translated text] 、 [translated text] [translated text] 、 [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] ） [translated text] [translated text] [translated text] [translated text] ：

```markdown
# [translated text]

 [translated text] [translated text] [translated text] 。

 [translated text] [translated text] [translated text] 。

- [translated text] [translated text] 1
- [translated text] [translated text] 2

 [translated text] [translated text] [translated text] [translated text] 。
```

## [translated text] [translated text]

| [translated text] [translated text] | [translated text] [translated text] |
|----------|----------|
| `<b> [translated text] </b>` | `** [translated text] **` |
| `<i> [translated text] </i>` | `* [translated text] *` |
| `<s> [translated text] </s>` [translated text] `<del> [translated text] </del>` | `~~ [translated text] ~~` |
| `<span style="color: #ff0000">` | `<span style="color: rgb(255, 0, 0)">` |
| `<span style="color: red">` | `<span style="color: rgb(255, 0, 0)">` |
| `<!-- center --> [translated text] <!-- /center -->` | `<!-- center:start -->\n [translated text] \n<!-- center:end -->` |
| `@ [translated text] <!-- mention:... -->`（ [translated text] [translated text] ） | `@ [translated text] <!-- mention:... -->`（ [translated text] [translated text] ） |
| `[ [translated text] ](url) <!-- linkPreview -->`（ [translated text] [translated text] ） | `[ [translated text] ](url)<!-- linkPreview -->`（ [translated text] [translated text] ） |
| 2 [translated text] [translated text] [translated text] [translated text] | 4 [translated text] [translated text] [translated text] [translated text] |
| `:smile:`（ [translated text] [translated text] ） | `:SMILE:`（ [translated text] [translated text] [translated text] [translated text] ） |
| `![ [translated text] ](url)<!-- [translated text] uuid -->`（ [translated text] [translated text] UUID） | `![ [translated text] ](url)`（ [translated text] [translated text] [translated text] [translated text] [translated text] UUID） |
| `![ [translated text] ](url)<!-- linkPreview -->` | `<!-- linkPreview -->` [translated text] [translated text] `[ [translated text] ](url)` [translated text] |
| `<td> [translated text] </td>`（`<td>` [translated text] [translated text] ） | `<td>\n\n [translated text] \n\n</td>`（ [translated text] [translated text] ） |

## [translated text] [translated text]

```markdown
# [translated text] [translated text]

## [translated text]

<!-- center:start -->
**Alpha [translated text] — [translated text] [translated text] **
<!-- center:end -->

 [translated text] [translated text] [translated text] [translated text] [translated text] ：

1. [translated text] [translated text]
 1. [translated text] [translated text]
 2. [translated text] [translated text]
2. [translated text] [translated text] [translated text]
 - [translated text] [translated text]
 - [translated text] [translated text]

### [translated text] [translated text]

| [translated text] | [translated text] [translated text] | [translated text] [translated text] |
|------|--------|--------|
| [translated text] [translated text] | 3.2s | **1.1s** |
| [translated text] [translated text] | 2.4% | <span style="color: rgb(53, 189, 75)">0.3%</span> |

### [translated text] [translated text]

```TypeScript
export function authenticate(token: string): boolean {
  return validateJWT(token);
}
```

> [translated text] ： [translated text] [translated text] [translated text] [translated text] <u> [translated text] [translated text] </u>。

- [x] [translated text] [translated text] [translated text] [translated text]
- [x] [translated text] [translated text]
- [ ] [translated text] [translated text] [translated text] [translated text]

 [translated text] [translated text] ：@ [translated text] <!-- mention:{"id":"lark_user_id_001","cn_name":" [translated text] ","en_name":"Zhang San","email":"zhangsan@example.com","blockType":"AT_USER_BLOCK"} -->@ [translated text] <!-- mention:{"id":"lark_user_id_002","cn_name":" [translated text] ","en_name":"Li Si","email":"lisi@example.com","blockType":"AT_USER_BLOCK"} -->

 [translated text] [translated text] ：[ [translated text] [translated text] ](https://example.com/sprint/42)<!-- linkPreview -->

:DONE: :DarkThumbsup:
```
