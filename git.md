# Git
---

## Présentation

Git est un logiciel libre de gestion de version. Il permet de conserver l'historique des modifications d'un fichier ou un projet. Il s'agit d'un système décentralisé fonctionnant en local et pouvant être synchronisé par un serveur git (Github, Gitlab...).

Pour mieux présenter Git et son utilité on va utiliser un exemple:
Imaginons deux étudiants, Alice et Bob, ils doivent réaliser un projet.Sans git chacun travail de son côté, puis s'envoient le code par Mail ou grâce à une clé usb.
Inconvénients ? C'est lent, long à faire, et parfois il peut y avoir des oublie (fichier oublier...)

Maintenant, Alice et Bob décident d'utiliser Git, comment ça se passe ?
Alice et Bob ont tous les deux le projet + git sur leur ordinateur, lorsqu'un des deux à fait un nouveau code, il envoie le commit à l'autre par mail, ainsi chacun peut travailler sur le même code plus simplement.

L'avantage de git c'est que chaque commit permet de créer des points de sauvegarde sur lesquels il est possible de revenir si besoin, ou annuler des modifications.
De plus, il est possible d'utiliser un serveur distant afin de faire des backup, et de synchroniser les personnes travaillant sur un même projet, c'est le principe derrière Github, Gitlab...

## Histoire et besoins

Pourquoi avoir créer Git ? Linus Torvalds, le créateur du noyau Linux, reçevait des patchs et des archives par mail des modifications effectués sur le noyau par d'ȧutres développeurs. Ce processus était long et fastidieux, afin de le simplifier certains membres de la communauté lui parlèrent d'un outil pour simplifier ce processus, les VCS (Version Control System).

C'est quoi un VCS ? C'est un système qui traque les changements de fichiers dans le temps pour permettre un retour sur une version précédente.

Il existe plusieurs types de VCS, le local:
- Vous copiez le répertoire et modifier la copie, ProjetV1, ProjetV2...
Mais c'est pas trop optimisé, on peut faire des erreurs et ça prend vite de la place pour rien.
Alors les développeurs ont commencé à ajouter une base de donnée qui traquerait les changements de fichiers.

- Les CVCS (Centralized Version Control System), dans ce cas c'est un serveur qui s'occupe de traquer les changements sur les fichiers, ça permet de fusionner plus facilement les fichiers, et de savoir qui travaille sur quels fichiers.

- Les DVCS (Distributed Version Control System), les fichiers sont traquées en local, une copie de la base de donnée et des fichier peut être envoyé sur un serveur distant ou à un autre utilisateur. Cela permet de pouvoir travailler à plusieurs, sans avoir besoin d'une connexion internet constante, et d'avoir des backups.

Pour les besoin du Kernel Linux, en 2002 Torvalds trouva un accord avec Bitkeeper, un DVCS, permettant aux membres de la communauté du Kernel Linux d'avoir une licence gratuite pour aider au développement du Kernel Linux. Mais en 2005, cet accord fût arrêté et la licence devint payante, Linus décida de créer son propre DVCS.

Ce nouveau DVCS devait répondre à plusieurs contraintes:
- Rapidité
- Simplicité
- Support de milliers de branches en parallèles (on en parlera plus tard)
- Capable de supporter des gros projets comme le Kernel Linux.

Ainsi Git sortira le 7 Avril 2005, puis en Juillet 2005, Linus annonca que les futures versions du noyau Linux seront développés avec Git.  
Et suite à cela, le dévellopement de Git sera laissé à Junio Hamano car Linus n'avait pas l'envie de maintenir Git.  

## Fonctionnement technique

Afin de bien comprendre les commandes à utiliser pour git, il est important de comprendre le fonctionement interne de git.

*Schéma Git*

Pour cela on va repartir sur notre exemple de projet entre Alice et Bob. Ils utilisent git afin de collaborer ensemble.
Alice fais des modifications sur le projet de son côté, elle commits ses modifications afin de créer un point de sauvegarde interne, permettant au besoin d'annuler et / ou voir les modifications.
Si elle veut que Bob ait accès à ses modifications alors elle doit soit envoyer le commit à Bob soit utiliser un serveur distant, comme Github. Ils ont choisi d'utiliser Github pour leur projet, donc afin d'envoyer son commit au serveur distant elle va le push.

Bob quant à lui, pour récupérer le commit d'Alice, il va pull depuis le serveur distant, et automatiquement les modifications d'Alice seront ajoutés à son projet local.

J'espère que vous avez fait un peu de C, et que vous comprennez ce qu'est un pointeur ? Sinon un pointeur c'est juste un variable pointant vers une autre variable.

Plus techniquement Git fonctionne avec un pointeur sur la branche de travail actuelle appelé HEAD, chaque branche contient une liste de commits.  
Un commit est lui même composé de tree. Tree qui sont des fichiers répertoriant les fichiers du projets, leurs contenu et d'autres tree.
Générallement les tree ne contiennent pas directement le contenu du fichier mais un pointeur vers le blob qui contiendra ce contenu.  
Un blob c'est un fichier binaire qui contient des données compressés (textes, images, vidéos).

Afin de réduire le temps de calcul et l'execution de la gestion de fichiers, git utilise un système d'instantanée. Au lieu d'enregistrer les modifications il fait des références vers les fichiers. Ainsi si le fichier est modifié, la référence sera vers le nouveau fichier.
Pour s'assurer de l'intégrité des fichiers, lors d'un ajout de fichier git calcul la somme de contrôle du fichier grâce à une empreinte SHA-1. Il s'agit d'une chaîne de 40 caractères hexadécimaux (0 à 9 et de A à F), calculé en fonction du contenu et de la structure du fichier.

Grâce à ce système s'il existe plusieurs fichiers avec un nom différent mais avec le même contenu alors git ne gardera en mémoire qu'un seul blob. Cela permet d'avoir une forte déduplication des fichiers et de gagner beaucoup de place.  
De plus lors d'une fusion entre 2 branches, ces calculs de somme de contrôle permettent de s'assurer qu'il n'y a pas de conflit sur les fichiers et sinon demander à l'utilisateur de régler le conflit manuellement.  

Pour informations ce système d'instantanée existe aussi pour votre ordinateur en général avec BTRFS, ce qui permet de gagner plusieurs gigaoctets de stockages.

## Présentation de quelques commandes de base

J'en ai déjà parler précédemment mais voici une version détaillé des commandes de base à utiliser sur Git.

### Actions local

`git init`: Initialise la base de donnée git
`git add`: Ajout un fichier dans le stagging area
`git rm`: supprime un fichier du stagging area
`git status`: affiche le status des fichiers, permet de savoir les fichiers qui ont été ajoutés ou non
`git diff`: permet de voir les modifications sur les fichiers depuis le dernier commit
`git commit -m message`: créer un commit, point de sauvegarde des fichiers dans la stagging area vers la base de donnée git
`git log`: permet d'afficher la liste des commits avec leurs messages, heures / dates

### Actions avec le serveur

`git push`: Envoi les commits vers le serveur Git (Github, Gitlab, Gitea, Forjego, Codeberg...)
`git pull`: Récupère les commits depuis le serveur Git
`git clone`: Télécharge un projet git depuis le serveur Git.

## Travailler à plusieurs avec git

Git est un super outils pour travailler tout seul, mais il est encore meilleur pour travailler à plusieurs.
Il permet de transmettre des fichiers, modifications beaucoup plus rapidemment qu'avec d'autres méthodes de travail, mais du fait de son fonctionnement il est possible d'avoir de nombreux soucis lorsqu'on travaille à plusieurs avec Git, nous allons voir comment les éviter.

Lorsque deux personnes modifient le même fichier et font un commit, git va avoir un conflit, il ne sera pas laquel version gardé, et pour cette raison il faudra que l'un des deux récupère le commit de l'autre et les modifications du fichier, puis fasse le tri.

Un moyen de retarder ce problème est d'utiliser des branches. Une branche c'est une copie du projet à instant T, à partir duquel les modications seront ajoutés.
Les branches peuvent aussi servir à développer des fonctionnalités et garder une version fonctionnelle pouvant être modifier / corrigé.

De plus, de nombreux systèmes d'exploitations et éditeurs aiment bien ajouter leurs fichiers lors de l'execution ou de la compilation (les répertoire .vscode...), il est possible de les exclure de git grâce au fichier .gitignore. Comme son nom l'indique, ce fichier permet de définir les fichiers que git doit ignorer.

Une fois que les modifications / fonctionnalités voulues sont finis sur notre branche, il faut la fusionner avec sa branche d'origine, on appelle ça merge. C'est à ce moment là qu'il peut y avoir des conflits et qu'il faudra probablement mettre la main dans le code afin de vérifier les parties qui peuvent causer problème et les corriger.

### Commandes

`git branch`: permet d'afficher le nom de la branche courante ainsi que la liste des branches
`git branch nom`: permet de créer une branche dérivée de la branche courante avec ce nom
`git checkout nom`: permet de changer de branche et d'aller sur la branche à ce nom
`git merge nom`: permet de fusionner la branche à ce nom


## Automatisation CI / CD

CI (Continuous Integration) ou intégration continue en français, est un processsus automatique pour facilité la fusion, les tests et les vérifications. Sur des gros projets ça peut éviter des nombreux problèmes, permettre de corriger des bugs simple et d'harmoniser le style de code.
CD (Continuoous Delivery / Deployment) ou déploiement continu en français, est un processus automatique pour faciliter le déploiement et la mise en place de notre applications, ça permet de s'assurer que la mise en production ne posera pas de soucis.

## Travail pratique

Afin de vous familiarisez avec Git, je vous invite à réaliser un petit projet. Chacun devra se connecter sur ce serveur git distant, récupérer le projet et créer une branche.
Sur cette branche vous devrez créer un fichier appelé `README.md`, et écrire quelques trucs en markdown.

Puis vous devez commit votre fichier et push votre branche sur le serveur git distant.

Une fois que vous avez finis cela, merger la branche de votre voisin de droite sur la votre, et faite une modification, puis commit et push.

Voilà maintenant vous maitriser les commandes de git, vous pourrez enfin partager du code plus simplement et efficacement dans vos projets de groupes.
