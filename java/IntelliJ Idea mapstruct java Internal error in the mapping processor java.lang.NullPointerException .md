# IntelliJ Idea mapstruct java: Internal error in the mapping processor: java.lang.NullPointerException - Stack Overflow
After upgrading to the version 2020.3 of Idea i get a NullPointerException for the mapping processor. If anybody has a clue... Thank you!

asked Dec 2, 2020 at 16:44

\[

![](https://i.stack.imgur.com/I3rf0.jpg?s=64&g=1)

]([https://stackoverflow.com/users/1668554/antonio](https://stackoverflow.com/users/1668554/antonio))

The solution is to update MapStruct to 1.4.1.Final or later version, [see this issue](https://github.com/mapstruct/mapstruct/issues/2215) for more details.

You can also add `-Djps.track.ap.dependencies=false` at File | Settings (Preferences on macOS) | Build, Execution, Deployment | Compiler | Build process VM options as a workaround.

answered Dec 2, 2020 at 17:55

\[

![](https://github.com/GXhunter/myNote/blob/main/2022-2-22%2015-34-03/c05902cc-91ac-4569-82ad-b15e27daa9e8.jpeg?raw=true)

]([https://stackoverflow.com/users/104891/crazycoder](https://stackoverflow.com/users/104891/crazycoder))

[CrazyCoder](https://stackoverflow.com/users/104891/crazycoder)CrazyCoder

367k152 gold badges932 silver badges837 bronze badges

5

If you cannot update MapStruct (my case), [details here](https://github.com/mapstruct/mapstruct/issues/2215).

You have to find the exact place where put the `-Djps.track.ap.dependencies=false` setting (and I had hard time to figure out where).

So, as they say, a picture is worth a thousand words:

[![](https://i.stack.imgur.com/QyDMc.png)
](https://i.stack.imgur.com/QyDMc.png) **Preferences --> Build, Execution, Deployment --> Compiler**

answered Apr 26, 2021 at 15:42

\[

![](https://github.com/GXhunter/myNote/blob/main/2022-2-22%2015-34-03/16dfc4f8-7bb8-4011-bb75-5b5df1334dd5.jpeg?raw=true)

]([https://stackoverflow.com/users/336827/freedev](https://stackoverflow.com/users/336827/freedev))

[freedev](https://stackoverflow.com/users/336827/freedev)freedev

19.9k4 gold badges92 silver badges106 bronze badges

1

There are several intellij bugs here. If I change the maven version 1.3.0.Final to 1.4.1.Final the intellij don't want to update the version.

**Solution**

1.  Upgrade the maven version to 1.4.1.Final
2.  Delete manually mapstruct 1.3.1.Final from External Libraries(Project Structure | Libraries)
3.  mvn clean package
4.  Click reload all maven projects

It works without -Djps.track.ap.dependencies=false

If you have swagger exclude mapstruct first:

```
<dependency>
<groupId>io.springfox</groupId>
<artifactId>springfox-swagger2</artifactId>
<version>${swagger2.version}</version>
<exclusions>
    <exclusion>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId>
    </exclusion>
</exclusions>

```

answered Dec 11, 2020 at 10:03

\[

![](https://github.com/GXhunter/myNote/blob/main/2022-2-22%2015-34-03/35d19b38-1e90-493c-9d3c-fd41eec409cd.png?raw=true)

]([https://stackoverflow.com/users/744839/sisak](https://stackoverflow.com/users/744839/sisak))

[Sisak](https://stackoverflow.com/users/744839/sisak)Sisak

5291 gold badge4 silver badges14 bronze badges

Expanding on [freedev's](https://stackoverflow.com/a/67269707/6224662) answer. For windows users the following will be the place to go :

1.  Click on the settings gear icon on the right. [![](https://i.stack.imgur.com/icqQw.png)
    ](https://i.stack.imgur.com/icqQw.png)
2.  Go to settings (ctrl+A+S is a good shortcut to get here).
3.  Find Compiler under Build,Execution, Deployment in the left menu [![](https://i.stack.imgur.com/Ur9GF.png)
    ](https://i.stack.imgur.com/Ur9GF.png)
4.  Add `-Djps.track.ap.dependencies=false` in User-local build process VM options

answered Oct 12, 2021 at 17:52

\[

![](https://i.stack.imgur.com/dGqKn.png?s=64&g=1)

]([https://stackoverflow.com/users/6224662/fireball-1](https://stackoverflow.com/users/6224662/fireball-1))

[fireball.1](https://stackoverflow.com/users/6224662/fireball-1)fireball.1

1,1491 gold badge12 silver badges31 bronze badges

0

In my case, I made these changes and it works perfectly for me.

[![](https://i.stack.imgur.com/Ieo3J.png)
](https://i.stack.imgur.com/Ieo3J.png)

[![](https://i.stack.imgur.com/sBG6x.png)
](https://i.stack.imgur.com/sBG6x.png)

Then I just deleted the caches and rebuilt the entire project, maybe it wasn't necessary but that worked for me.

\[

![](https://i.stack.imgur.com/hXT0U.jpg?s=64&g=1)

]([https://stackoverflow.com/users/4826457/suraj-rao](https://stackoverflow.com/users/4826457/suraj-rao))

[Suraj Rao](https://stackoverflow.com/users/4826457/suraj-rao)

28.7k10 gold badges90 silver badges98 bronze badges

answered Dec 1, 2021 at 16:13

\[

![](https://github.com/GXhunter/myNote/blob/main/2022-2-22%2015-34-03/3104e90e-d152-489d-933f-ac0d14ec763e.jpeg?raw=true)

]([https://stackoverflow.com/users/12744735/codexp](https://stackoverflow.com/users/12744735/codexp))

Not the answer you're looking for? Browse other questions tagged [intellij-idea](https://stackoverflow.com/questions/tagged/intellij-idea "show questions tagged 'intellij-idea'") [mapstruct](https://stackoverflow.com/questions/tagged/mapstruct "show questions tagged 'mapstruct'") or [ask your own question](https://stackoverflow.com/questions/ask).

* * *

 [https://stackoverflow.com/questions/65112406/intellij-idea-mapstruct-java-internal-error-in-the-mapping-processor-java-lang](https://stackoverflow.com/questions/65112406/intellij-idea-mapstruct-java-internal-error-in-the-mapping-processor-java-lang)
