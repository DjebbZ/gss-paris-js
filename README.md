## GSS, le CSS tel qu'il devrait l'être.

Paris.js #40 @Deezer, 29/10/2014.
Khalid Jebbari, @Dj3bbZ

##### Disclaimer :

Je ne suis pas un expert CSS, encore moins en GSS. Comme beaucoup de développeurs, je n'aime pas trop y toucher, je ne comprends pas toujours comment ça marche, et je trouve ça bizarre. Ce soir je vais essayer de vous expliquer pourquoi, et vous proposer une alternative.

Commençons tout de suite par la démo (`git checkout step1`). J'ai préparé une page simple. Un menu horizontal, un titre et une accorche, et 2 colonnes de recettes de cuisine suivies d'un footer. Rien d'extra-ordinaire a priori.

Le code : HTML a peu près sémantique. L'ordre d'écriture correspond à peu près à l'ordre d'affichage. Plusieurs balises "containers" : header, nav, section, footer.

CSS : J'ai utilisé (sûrement pas très bien) les floats. Sérieusement, c'est n'importe quoi. Quand je float .nav-item, je dois penser à `clear`er le float. Je l'ai fait sur .main, mais j'aurais pu le faire avec un "clearfix".

``` css
.container:before,
.container:after {
  content:"";
  display:table;
}
.container:after {
  clear:both;
}
.container {
  zoom:1; /* For IE 6/7 (trigger hasLayout) */
}
```

Source : http://stackoverflow.com/questions/211383/which-method-of-clearfix-is-best

On s'écarte complètement du problème "Je veux aligner des éléments horizontalement". On doit penser aux flux des éléments, aux marges qui disparaissent ("collapse"). Pourtant le sélecteur se lit "naïvement" : les éléments avec la classe .nav-item s'aligne de gauche à droite.

Étape 2 (`git checkout step2`) : avec `display: inline-block;`. Les float et clear ont disparu. Mais je dois quand même penser au flux des éléments puisqu'il faut penser à remettre le bon mode de display sur la dernière sinon le footer s'affiche au mauvais endroit. Pourtant le mode de display d'une recette n'a a priori rien à voir avec le footer.
Autre problème : rajouter 5 pixels à la largeur du container, sinon les éléments passent tous en dessous des autres.

J'aurai pu aussi essayer le mode `display: table`, mais ça m'aurait obligé à insérer des `ul` pour chaque ligne de contenu pour leur donner le mode `display: row`. Ce qui équivaut à penser en ligne un layout en colonne...

J'aurai pu aussi tout faire en `position: absolute`. Le problème c'est qu'on doit positionner les éléments par rapport à un repère global, pas par rapport aux autres. Du coup difficile de faire un layout qui s'adapte aux contenus.

Autre preuve du problème avec CSS : ~15 pages pour expliquer comment centrer (sur l'excellent blog *Putain de code !*) : http://putaindecode.fr/posts/css/le-guide-complet-pour-centrer-en-css

Tout ça pour dire :

Derrière un langage déclaratif se cache un langage impératif pour gérer les layouts. On doit manuellement changer et gérer les modes d'affichage dans un système qui mélange l'héritage/la hierarchie, le flux et le positionnement par coordonnées.

Modes d'affichage des éléments : float ? absolute ? relative ? fixed ? wtf!? Je veux décrire la position de mes éléments les uns par rapport aux autres.

Flux : rend impossible la séparation du contenu et de sa présentation, car la présentation dépend complètement du code source. Argh !

W3C : donner des solutions haut-niveau au lieu de donner des outils bas-niveau. Variables ? Math ? Inspection du DOM ?

Sans parler des problèmes cross-browsers...

Pourtant il existe d'autres façons de faire du layout dans le monde du software. Ils peuvent être basés sur les coordonnées (Visual Studio, Delphi), ou sur la hierarchie de containers (Java Awt/Swing/FX/GXT/GXT, C++ Qt, ExtJS/Sencha en Javascript, Mozilla Xul, GTK+). Aucun ne se base sur un flux du document comme le CSS.

CCSS : Greg Badros, the author of the Cassowary Constraint Solver & recently retired Facebook VP, proposed Constraint CSS (CCSS) as a general solution for CSS layout. Back in '99 Badros [demonstrated](http://www.cs.washington.edu/research/constraints/web/ccss-uwtr.pdf) responsive layouts with CCSS.

Jamais adopté... jusqu'à Apple, OSX Lion (10.7) et [Cocoa Auto-Layout + VFL](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage/VisualFormatLanguage.html).

GSS : The Grid Stylesheets. http://gridstylesheets.org/

Démo (`git checkout step3`).

GSS outrepasse complètement le moteur de layout du navigateur et propose de gérer les layouts par contraintes. QUESTION : qui connaît Prolog ? Core.logic en Clojure ? MiniKanren ? La programmation logique par contrainte ?

Disclaimer : c'est la 1ère fois que j'utilise GSS, je ne prétends pas que la solution que je vous propose soit la bonne. Mais l'approche est suffisamment innovante et intéressante pour être montrée.

Contrainte = relation.

relation != assignement

en JS (ou autre)

x = y;
y = 10
x = 100;

en programmation par contrainte

x == y;
x == 100;
y >= 10;


Liens :
http://gridstylesheets.org/
https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage/VisualFormatLanguage.html
http://www.sitepoint.com/give-floats-the-flick-in-css-layouts/
http://ajaxian.com/archives/the-fundamental-problems-with-css13
http://c2.com/cgi/wiki?CoordinateVersusNestedGui
http://www.cs.washington.edu/research/constraints/web/ccss-uwtr.pdf
http://putaindecode.fr/posts/css/le-guide-complet-pour-centrer-en-css/
