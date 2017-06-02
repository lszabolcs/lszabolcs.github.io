---
layout: post
title: Biztonságos Wordpress?
permalink: /2017-06-02-biztonsagos-wordpress/
---

A Wordpress-re leggyakrabban használt jelzők között biztosan szerepel ez a kettő: legelterjedtebb, nem túl biztonságos. Mondhatni, hogy a kettő kéz a kézben jár. Sajnos a nagy szabadság nagy kockázattal is jár, gondoljunk csak bele: alapértelmezetten adminfelületről még a sablonok, bővítvmények .php fájlai is [szerkeszthetőek](https://codex.wordpress.org/Editing_Files){:target="_blank"}, sőt kiskapukkal a teljes forráskód is.

## Fejlesztői checklist
### Frissítsünk
Folyamatosan telepítsük a frissítéseket (ugyan ez már automatikus, de a bővítményekről és témákról nekünk kell gondoskoni).


### Ne módosítsuk a core fájlokat
Ha bele akarunk nyúlni a WP működésébe, akkor írjunk plugint és használjuk a [Plugin API](https://codex.wordpress.org/Plugin_API){:target="_blank"}-t.


### Kapcsoljuk ki a beépített fájlszerkesztőt__
Egy sor a `wp-config.php`-ben.
~~~ php
define('DISALLOW_FILE_EDIT', true);
~~~


### Korlátozzuk a felhasználók jogait
Minden felhasználónak a lehető legkevesebb jogot adjunk (amennyi éppen kell a munkavégzéséhez). Az adminisztrátori jogokkal rendelkező felhasználónak ne legyen "admin" a neve, és ne publikáljunk vele tartalmat.


### Szigorítsuk a belépés feltételein
A `wp-login.php`-nál korlátozzuk a belépések számát és rakjunk be kétlépcsős azonosítást.


### Az adatbázisban használjuk alternatív tábla előtagot
Ez is csupán egy sor a `wp-config.php`-ben, viszont utána az adatbázisban is ügyeljünk a táblák frissítésére.
~~~ php
$table_prefix  = 'sampleprefix_';
~~~


### Használjunk PROXY-t
Vagy valamilyen egyéb CDN-es biztonsági szolgáltatást, mint például egy sima ingyenes Cloudflare fiókot (ingyenes megosztott SSL-lel).


### Állítsuk be a Google Search Console-t
Ez nem csak a keresőoptimalizálásban segít, hanem azt is jelzi, ha valamilyen rosszindulató kód futna az oldalunkon.


### Ügyeljünk a mappákra
A mappáknak 755-ös, a fájloknak 644-es hozzáférési jogot állítsunk be és használjunk [alternatív mappa struktúrát](https://www.rarst.net/wordpress/directory-structure/){:target="_blank"}.


### Korlátozzuk a fájlok külső forrásból való elérhetőségét
Korlátozzuk vagy [jelszavazzuk](https://codex.wordpress.org/Brute_Force_Attacks#Password_Protect_wp-login.php){:target="_blank"} le a `/wp-admin` mappát. A `/wp-includes`-ban olyan fájlokat tárolunk, amik a felhasználói oldal számára nem kellenek, így innen ne engedjünk futtetni semmit se. A `.htaccess` fájl, WP-s blokkján kívülre írjuk:
{% highlight apache %}
# Block the include-only files.
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^wp-admin/includes/ - [F,L]
RewriteRule !^wp-includes/ - [S=3]
RewriteRule ^wp-includes/[^/]+\.php$ - [F,L]
RewriteRule ^wp-includes/js/tinymce/langs/.+\.php - [F,L]
RewriteRule ^wp-includes/theme-compat/ - [F,L]
</IfModule>
# BEGIN WordPress
{% endhighlight %}

Hozzunk létre az `/uploads` mappában is egy `.htaccess`-t, így elkerülve, a feltöltött fájlok futtatását:
~~~
# Kill PHP Execution
<Files *.php>
deny from all
</Files>
~~~


### Válogassuk meg a bővítményeket és témákat
Csak megbízható forrásból (ellenőrzött fejlesztőtől) telepítsünk bővítményt és témát (és lehetőleg minél kevesebbet).


### Ne spóroljunk a hostingon
Válasszunk megfelelő hosting szervert (nem árt ha van már tapasztalatuk WP-s oldallal) és legyen biztonsági mentésünk az adatbázisról és a fájlokról is.


## Biztonsági pluginek
A fentiek nagy részét (sőt, még többet is) elintézhetunk speciális pluginekkel is. Érdemes közüllök többet is megnézni és a nyújtott szolgáltatások vagy a felület használhatósága alapján választani:
- [Sucuri Scanner](https://wordpress.org/plugins/sucuri-scanner/){:target="_blank"}
- [iThemes Security](https://wordpress.org/plugins/better-wp-security/){:target="_blank"}
- [JetPack](https://wordpress.org/plugins/jetpack/){:target="_blank"}
- [All In One WP Security and Firewall](https://wordpress.org/plugins/all-in-one-wp-security-and-firewall/){:target="_blank"}

## Hivatkozások
- [Wordpress Codex - Hardening Wordpress](https://codex.wordpress.org/Hardening_WordPress){:target="_blank"}
- [Wordpress Codex - Plugin API](https://codex.wordpress.org/Plugin_API){:target="_blank"}
- [Wordpress Codex - Brute Force Attack](https://codex.wordpress.org/Brute_Force_Attacks){:target="_blank"}
- [Wordpress Codex - Editing Files](https://codex.wordpress.org/Editing_Files){:target="_blank"}
- [Wpmudev - Ultimate Wordpress Security Checklist](https://premium.wpmudev.org/blog/ultimate-wordpress-security-checklist/){:target="_blank"}
