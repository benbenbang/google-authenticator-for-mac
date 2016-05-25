## Google Authenticator for Mac!!

Note： 取之於人太多，希望能開始盡量回饋一些。從開始學習R, Python，慢慢開始到使用Linux, 架設Ubuntu等等，無論從Github、Stack Overflow等學習了不少，這次為了善用家中原有的電腦來做運算，除了VPN之外，再來就是一些資安問題。於是除了Passwordless SSH之外，其次就是希望在非得使用他人電腦時能有其他防護，因此，在Mac上使用了Google的兩步驟驗證。

目前市面上提供兩步驟驗證的API挺多的，Authy就是一個我蠻喜歡的服務，然而大部分API雖然都支援SSH，但多是Ubuntu或Linux，但使用如家中電腦規格的VPS都要價不匪，目前資料量和產出也沒有必要用到Google Cloud或AWS等，因此就繼續物盡其用，把Google的兩步驟驗證加入Mac吧。

使用的兩步驟驗證軟體可用如：

1. Google Authenticator
   * [iOS Version](https://itunes.apple.com/tw/app/google-authenticator/id388497605)
   * [Android Version](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2)
2. Authy （這是我偏好的軟體，同樣支援各平台兩步驟驗證，也比較精美）
   * [iOS Version](https://itunes.apple.com/us/app/authy/id494168017)
   * [Android Version](https://play.google.com/store/apps/details?id=com.authy.authy)
   * [Chrome App](https://chrome.google.com/webstore/detail/authy-chrome-extension/fhgenkpocbhhddlgkjnfghpjanffonno)

---
## 安裝步驟
**Caution: 本使用步驟在筆者電腦可正常運行，但不保證本教學完全適用任何情況，風險請評估後自行承擔。**  
### Step 1. Google Authenticator安裝
``` terminal
$ git clone https://github.com/google/google-authenticator.git
# 備份：http://github.com/benbenbang/google-authenticator-for-mac.git
```
``` terminal
$ cd google-authenticator/libpam
$ autoreconf -ivf
<snip>
$ touch AUTHORS NEWS README ChangeLog
$ automake --add-missing
<snip>
$ ./configure
<snip>
$ sudo make install
<snip>
```
``` terminal
$ sudo cp /usr/local/lib/security/pam_google_authenticator.so /usr/lib/pam/
# 進入vim修改
$ sudo vi /etc/pam.d/sshd
# 增加: auth required pam_google_authenticator.so
# vim: i for insert, exit見文末Note
```
``` terminal
# 備份 /etc/sshd_config
# Linux位置: /etc/ssh/sshd_config
$ sudo cp /etc/sshd_config /etc/sshd_config_backup
$ sudo vi /etc/sshd_config
# #ChallengeResponseAuthentication yes → ChallengeResponseAuthentication yes
```

### Step 2. 設定
``` terminal
$ google-authenticator

# Terminal 回覆的網址貼去瀏覽器，用Authy或Google Auth掃描（iOS或Android皆有）
# 本步驟的資訊很重要，請妥善保管
> Do you want authentication tokens to be time-based (y/n) y
https://www.google.com/chart?chs=200x200&chld=M|0&cht=qr&chl=
otpauth://totp/user@mini.local%3Fsecret%3DABCDEFGHIJKLMNOP
Your new secret key is: ABCDEFGHIJKLMNOP 
Your verification code is 000000
Your emergency scratch codes are:
  00000000
  00000000
  00000000
  00000000
  00000000

> Do you want me to update your "/Users/user/.google_authenticator" file (y/n) y
```
``` terminal
# 詢問1Token是否於時效內可多次使用
> Do you want to disallow multiple uses of the same authentication
token? This restricts you to one login about every 30s, but it increases
your chances to notice or even prevent man-in-the-middle attacks (y/n) y
```
``` terminal
# 詢問是否同意接受輸入Token時，給予30±t的時間差
> By default, tokens are good for 30 seconds and in order to compensate for
possible time-skew between the client and the server, we allow an extra
token before and after the current time. If you experience problems with poor
time synchronization, you can increase the window from its default
size of 1:30min to about 4min. Do you want to do so (y/n) y
```
``` terminal
# 詢問是否同意保護機制
> If the computer that you are logging into isnt hardened against brute-force
login attempts, you can enable rate-limiting for the authentication module.
By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting (y/n) y
```

### Step 3. 測試
``` terminal
# 大功告成
# 遠端時會呈現
> ssh username@host
[Verificaiton code:          ] # 查詢動態碼
[Password:                   ] # user密碼
# 成功！
```

---
## Notes: 
1. 關閉Vim  
  Hit the Esc key; that goes into command mode. Then you can type
  * `:q` to quit (short for `:quit`)
  * `:q!` to quit without saving (short for `:quit!`)
  * `:wq` to write and quit (think write and quit)
  * `:wq!` to write and quit even if file has only read permission (if file does not have write permission: force write)
  * `:x` to write and quit (shorter than `:wq`)
  * `:qa` to quit all (short for `:quitall`)
  When you press `:`, a `:`will appear at the bottom of the screen.
  Or you can press `Esc` `ZZ` (`Esc` `Shift+Z` `Shift+Z`) to write/save if the file was changed, then quit.
  Or if you don't want to save changes you can use ZQ instead.
  Vim has extensive help, so type `Esc:help Return` and you will have all your answers and even a neat tutorial.

---
+2-Steps-Authentication+, +兩步驟驗證+, +Mac+, +Google-Authenticator+
