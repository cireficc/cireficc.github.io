---
layout: post
title: "Pourquoi je commence un blog"
description: "Des raisons pour lesquelles je commence à tenir un blog"
modified: "Sat Apr 26 2016 15:53:00 GMT-0500 (Eastern Standard Time)"
category: personal
comments: true
published: true
categories: 
  - personal
featured: true
---

Ces dernières 5 ans de mes études supérieures, je n'ai pas beaucoup fait pour documenter mon travail, comment j'ai résolu des problèmes,
ou simplement d'avoir une place pour parler de ce qui m'intéresse. Et je me suis rendu compte que j'ai peu de présence web à part mon profil
Linkedin et certaines activités sur des sites comme [Stack Exchange](http://stackexchange.com/users/2255622/chris-cirefice). Pour résoudre ces
problèmes, j'ai décidé de me mettre à tenir un blog.

Ce faisant me permettrai d'avoir une archive personelles des choses que j'avais accompli, et comment je les ai accomplies.
Également, mes billets de blog mau sujet de la programmation seraient utiles pour d'autres développeur à l'avenir. Mes billets de
blog au sujet des langues comme le japonais seraient utiles pour ceux qui apprennent la langue et ont de la difficulté avec la grammaire.
Le but est de renforcer mes propres connaissances de ces sujets ainsi que fournir *de l'information* et *de l'aide* pour d'autres
personnes, dont je l'apprécie depuis quelques années comme j'apprends de plus en plus.

Dernièrement, il se peut que des futurs employeurs tombent sur mon blog et se feront une meilleure idée de comment je surmonte
des épreuves de programmation, et d'avoir une meilleure compréhension de mes capacités de communication. Cela me bénéficiera
naturellement, mais cela n'est pas l'objectif principal.

Après avoir décidé de commencer un blog, j'ai eu du mal à comprendre quels outils étaient les meilleurs pour en faire. J'ai eu
quelques conditions nécessaires que j'ai [documenté il y a longtemps](http://softwarerecs.stackexchange.com/questions/7519/multilingual-blogging-platform):

1. **Une platforme légère** - *Wordpress* n'était tout simplement pas une option
2. **Intégration avec SCM** (par exemple, Github, Bitbucket) - si je n'avais pas une historique des révisions, ce serait tombée à l'eau
3. **Formatage de code** - c'était absolument **nécessaire**, parce qu'au moins la moité de mes articles seraient associés au code
4. **Hébergement, soit à faible coût, soit gratuit** - Je ne voulais pas avoir besoin ni de mon propre serveur pour le blog, ni de beaucoup payer pour ça
5. **Contrôle de contenu complet** - Je voulais être capable de façoner la plateforme ou le système pour répondre à mes besoins
6. **Multilingual content capability** - Je devais être capable de traduire en langues différentes les articles pour en améliorer l'accès
7. **Custom domain name** - Bien que ce ne soit pas incontournable, ayant l'option d'avoir une domaine personnalisé pour en maximiser l'audience est toujours une bonne idée

Il y avait plus de besoins, mais ce sont les principaux. Après avoir passé beaucoup de temps à rechercher, j'ai trouvé finalement la combinaison
parfaite: [Jekyll](https://jekyllrb.com) + [pages Github](https://pages.github.com)! Cette combinaison permet d'écrire des articles en
[Markdown](https://en.wikipedia.org/wiki/Markdown) et un langage modèle pesudo-Ruby qui s'appelle [Liquid](http://liquidmarkup.org),
générant des belles pages HTML sans tout l'HTML merdique (qui veut écrire en code HTML?). Et bien sûr que je ne suis point développeur web
et que j'ai peu de créativité en ce qui concerne le web design, et donc j'ai eu aussi bien d'un thème pour le blog. Après un peu de recherhe,
j'ai trouvé un super thème qui s'appelle [Notepad](https://github.com/hmfaysal/Notepad) créé par le superbe [Hossain M. Faysal](https://twitter.com/hmfaysal).

J'ai installé le blog sur [Cloud9](https://c9.io) (un IDE web) et j'ai commencé à écrire cet article! Je m'occuperai un peu plus de la personnalisation
plus tard quand j'ai du temps (*que disent toujours tous les programmeurs*) mais pour l'instant, cette solution est très bonne.

I got the blog all set up on [Cloud9](https://c9.io) (a web IDE) and started writing this post! I'll get to customizing things a bit more
later when I have the time (*said every programmer ever*) but for now, this solution is a really good one.

*[SCM]: gestion du code source
*[SEO]: optimisation des moteurs de recherche
*[IDE]: environment de développement interactif