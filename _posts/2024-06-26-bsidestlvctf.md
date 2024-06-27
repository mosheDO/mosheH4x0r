---
title: bsidestlv
published: true
---


## Dungeon

**Name:** _Dungeon_ <br>
**Description:** Enter the Dungeon to find the Pixelated Flag! It has been broken to 3 pieces, can you find them all?
<br>
**Category:** Reverse Engineering

**Author: Liam Troper**

This one was quite defecault but after amount of time & effort I finally did that.

when unzipping the challenge a huge amount of code is revealed it was pretty large and I even didn't know were to start while there is a readme in this folder which showed a title and a link to a site

**Shattered Pixel Dungeon** and the link [Shattered Pixel Dungeon](https://shatteredpixel.com/shatteredpd/)
so I enter to the site and downloaded the code from GitHub.

thinking of this as a patch analysis so `diff` command for the rescue

```zsh
➜  Dungeon git:(master) ✗ diff -r shattered-pixel-dungeon differ
Only in shattered-pixel-dungeon: .git
Only in differ: .gradle
Only in differ: .idea
Only in differ: ClickMeToPlay.jar
Only in differ: ClickMeToPlay.jar.cache
Only in differ/SPD-classes: build
diff --color -r shattered-pixel-dungeon/build.gradle differ/build.gradle
16,17c16,17
<         appName = 'Shattered Pixel Dungeon'
<         appPackageName = 'com.shatteredpixel.shatteredpixeldungeon'
---
>         appName = 'BSides Shattered Pixel Dungeon'
>         appPackageName = 'com.bsides.bsidesshatteredpixeldungeon'
Only in differ/core: .gradle
Only in differ/core: build
diff --color -r shattered-pixel-dungeon/core/src/main/assets/messages/journal/journal.properties differ/core/src/main/assets/messages/journal/journal.properties
8,9c8,9
< journal.document.adventurers_guide.identifying.title=Identifying Items
< journal.document.adventurers_guide.identifying.body=You won't know all of the properties of some items when you first find them.\n\nThe colors of potions and symbols on scrolls are different in each dungeon. Unidentified equipment can be upgraded or enchanted if you're lucky, or it might be cursed!\n\nScrolls of identify, upgrade, or remove curse are very useful if you want to reduce the risk of using unidentified equipment.\n\n(You can find a list of all the items you've identified in the items tab of your journal)
---
> journal.document.adventurers_guide.identifying.title=Identifying Items And Flags (BSides Edition)
> journal.document.adventurers_guide.identifying.body=You won't know all of the properties of some items when you first find them.\n\nThe colors of potions and symbols on scrolls are different in each dungeon. Unidentified equipment can be upgraded or enchanted if you're lucky, or it might be cursed!\n\nScrolls of identify, upgrade, or remove curse are very useful if you want to reduce the risk of using unidentified equipment.\n\n(You can find a list of all the items you've identified in the items tab of your journal)\n\n(H30n)
Binary files shattered-pixel-dungeon/core/src/main/assets/sprites/items.png and differ/core/src/main/assets/sprites/items.png differ
diff --color -r shattered-pixel-dungeon/core/src/main/java/com/shatteredpixel/shatteredpixeldungeon/Dungeon.java differ/core/src/main/java/com/shatteredpixel/shatteredpixeldungeon/Dungeon.java
220c220,246
<
---
>
>
>     public static String customDecrypt(char[] encText, String key) {
>         StringBuilder result = new StringBuilder();
>         int keyIndex = 0;
>         int keyLength = key.length();
>
>         for (int i = 0; i < encText.length; i++) {
>             char encChar = encText[i];
>             int k = key.charAt(keyIndex);
>             char decChar = (char) (((encChar - i) ^ k) - i % 256);
>             result.append(decChar);
>             keyIndex = (keyIndex + 1) % keyLength;
>         }
>
>         return result.toString();
>     }
>
>     public static void main(String[] args) {
>         char[] encText = {'3', '', '*', 'b', '/', 'K', 'h', '\\', 'Ñ', 'b', ')', 'b', '8', 'S', '/', ''};
>               String ky = "items + journal + key";
>
>         String decryptedText = customDecrypt(encText, ky);
>         System.out.println("Decrypted: " + decryptedText);
>       }
>
>
diff --color -r shattered-pixel-dungeon/core/src/main/java/com/shatteredpixel/shatteredpixeldungeon/items/keys/SkeletonKey.java differ/core/src/main/java/com/shatteredpixel/shatteredpixeldungeon/items/keys/SkeletonKey.java
36c36,38
<
---
>
>       private static String k3 = "U1ci";
>
Only in differ/desktop: .gradle
Only in differ/desktop: build
Only in differ/ios: robovm.properties
Only in differ: local.properties
Only in differ/services: build
Only in differ/services/news/shatteredNews: build
Only in differ/services/updates/githubUpdates: build

```

There is a decrypt function `customDecrypt(char[] encText, String key)` while running it in a java env with `String ky = "items + journal + key";`
we get Gibberish 


```
Decrypted: ZpK/TaCn?-`6?B
```
So sure this isn't correct checking `items.png` in HexDump didn't reveal much but sure it has some changes after checking carefully the differences between both images we found the change



![](https://raw.githubusercontent.com/mosheDO/mosheH4x0r/master/assets/2024-06-26/2024-06-26-125211.png)

so we can see `P4N7` but the image is called `items.png` and the key is `String ky = "items + journal + key";` so we got items now we need `journal` and `key` both of them is in the `diff` output

```zsh
\n\n(H30n)

>       private static String k3 = "U1ci";
```

![](https://raw.githubusercontent.com/mosheDO/mosheH4x0r/master/assets/2024-06-26/2024-06-26-125745.png)

combing them all we get 
`String ky = "P4N7H30nU1ci";` after running this we get this `c0de_pL4?_r3pea7`
the question mark doesn't fit so we change it to `y` `c0de_pL4y_r3pea7` adding the prefix and we got the flag

`BSidesTLV2024{c0de_pL4y_r3pea7}`