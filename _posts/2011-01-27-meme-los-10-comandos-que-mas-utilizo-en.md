---
title: 'Descubre cuales son los 10 comandos que más usas en Linux con esta línea'
layout: post.amp
permalink: /meme-los-10-comandos-que-mas-utilizo-en/
modified: 2016-08-30T10:30
categories:
  - meme
main-class: "linux"
color: "#2196F3"
---

En <a target="_blank" href="http://www.ubuntizandoelplaneta.com/2011/01/meme-los-10-comandos-que-mas-utilizo.html">ubuntizando el planeta</a> he leido este meme que voy a publicar, que consiste en ejecutar la orden

```bash
history | awk '{print $2}' | sort | uniq -c | sort -rn | head -10
```

en nuestro terminal para saber cuales son los comandos que más usamos en linux, seguro que os sorprendéis al ver vuestros resultados.

<!--ad-->

Invito a participar en el meme a:

<a target="_blank" href="http://usemoslinux.blogspot.com/">Usemos Linux</a>
<a target="_blank" href="http://www.nosolounix.com/">No solo Unix</a>

Aqui mi salida en mi máquina [Gentoo](/tags/#gentoo "Artículos sobre Gentoo"):

```bash
hkr@alex ~ $ history | awk '{print $2}' | sort | uniq -c | sort -rn | head -10
    217 git
    130 cd
    106 sudo
    103 ll
     70 gp # Un alias a git push
     26 sshkey
     26 eval # Para lanzar ssh-agent
     23 blog # Alias para lanzar el editor del blog (Emacs)
     18 eselect
     17 equery
```
