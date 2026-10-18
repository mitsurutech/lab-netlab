# Labs de réseau dans une seule VM : routage statique, OSPF et BGP avec FRRouting

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml)

Trois **labs de réseaux informatiques** prêts à l’emploi pour la classe, proposés par la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les écoles et les établissements donnent à chaque élève ses propres machines et son terminal dans le navigateur. Chaque élève reçoit sa propre VM Ubuntu, dans laquelle est construit un petit réseau, avec des **routeurs qui exécutent FRRouting** et des hôtes depuis lesquels faire des ping, et apprend le **routage statique, OSPF et BGP** sur la même console `vtysh` que celle qu’il rencontrera sur du matériel réel. Rien n’est installé sur l’ordinateur de l’élève et aucune licence Cisco n’est nécessaire.

Un routeur a besoin d’une machine à lui et un élève n’a qu’une VM : chaque routeur et chaque hôte est donc un **espace de noms réseau** (network namespace), chaque liaison une paire de câbles virtuels, et chaque routeur exécute ses propres démons FRRouting. Les vraies commandes fonctionnent, et c’est tout l’intérêt : `ip route`, `traceroute -n`, `tcpdump`, `vtysh`.

## Les trois labs

| Lab | Fichier | Ce que fait l’élève | Page d’aide |
| --- | --- | --- | --- |
| Routage statique | [`static-routing.lab.yaml`](static-routing.lab.yaml) | Trouver où s’arrête un paquet, ajouter des routes statiques sur les routeurs qui en ont besoin, garder un réseau de gestion inaccessible | [Lab de routage statique](https://mitsurutech.co.uk/fr/help/lab-netlab-static) |
| OSPF | [`ospf.lab.yaml`](ospf.lab.yaml) | Activer OSPF dans l’aire 0 sur trois routeurs, observer les messages hello, attendre que chaque adjacence soit Full, garder un réseau hors des annonces | [Lab OSPF](https://mitsurutech.co.uk/fr/help/lab-netlab-ospf) |
| BGP | [`bgp.lab.yaml`](bgp.lab.yaml) | Faire de deux systèmes autonomes des voisins, annoncer le réseau de chacun, et n’annoncer rien du réseau privé derrière l’un d’eux | [Lab BGP](https://mitsurutech.co.uk/fr/help/lab-netlab-bgp) |

Chaque lab laisse dans le dossier personnel de l’élève un README qui dit quoi faire et ne donne aucune des réponses. Chacun a une **vérification**, un script qui note une VM rendue sans que l’enseignant la lise : il s’exécute sur une copie de la machine enregistrée après un démarrage à neuf, de sorte qu’il ne voit que ce que l’élève a enregistré avec `write memory`, et il ne réussit que si l’état réel est bon (les routes sont dans les tables de routage, les voisins OSPF sont Full ou la session BGP est Established, `h1` joint `h2` en passant par les routeurs, et le réseau privé reste inaccessible).

## Ce que contient un fichier

Chaque `.lab.yaml` est une **définition de lab de VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 2 processeurs, 2048 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : aucun, le lab est le terminal |
| `setup` | un script exécuté une fois, en root, au premier démarrage de la VM de chaque élève : il installe FRRouting et `tcpdump` depuis l’archive d’Ubuntu en une dizaine de secondes, puis écrit la commande `netlab`, la topologie et le README du lab |
| `check` | le script de notation, exécuté en root sur une copie du disque enregistré après un démarrage à neuf ; son code de sortie est le résultat et `0` signifie réussite |

La commande `netlab` qu’il installe : `netlab status` liste les machines, `netlab shell r1` ouvre la console d’un routeur et `netlab shell h1` celle d’un hôte, et `netlab reset` recommence le lab.

## L’utiliser sur la plateforme

**Pour en donner un comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab de VM Ubuntu**.
2. Appuyez sur l’exemple dont vous voulez partir, ou collez le contenu de l’un des fichiers d’ici. Utiliser un exemple remplace le texte, et Annuler rétablit le vôtre.
3. Publiez et essayez d’abord votre propre copie. Chaque élève a sa propre VM, conservée d’une session à l’autre.
4. Distribuez-la comme n’importe quelle tâche. Quand elle est rendue, lancez la vérification sur la ligne d’un élève dans la liste de la classe : elle démarre une copie de sa machine enregistrée, qui n’est pas réenregistrée, et indique si elle a réussi et ce qu’elle a affiché.

Le [guide des labs de VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) du manuel et sa [page des labs d’exemple](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste. Pour créer votre propre topologie, copiez un fichier et modifiez la topologie dans son `setup` : une ligne par routeur, hôte et liaison, et `daemons ospfd` ou `daemons bgpd` pour démarrer ce protocole sur chaque routeur.

Pas encore de compte d’établissement ? [La première année est gratuite](https://mitsurutech.co.uk/fr/pricing) jusqu’à 50 élèves, et [l’inscription](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérification des fichiers

À chaque push, `.github/workflows/check.yml` vérifie que chaque fichier est du YAML valide avec les cinq clés ci-dessus et que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme. Il ne démarre pas de VM : cela se fait sur les propres machines de la plateforme.

## Ce qui ne fonctionne pas dans un lab

La VM atteint l’archive de paquets d’Ubuntu, PyPI, npm et GitHub, et rien d’autre : on ne peut donc pas pointer un lab vers un hôte sur Internet. C’est une seule VM, il n’y a donc pas de seconde machine depuis laquelle attaquer ou vers laquelle attaquer ; une topologie de plusieurs routeurs est construite à l’intérieur.

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`. Elle couvre les définitions de lab, l’outil `netlab` et cette documentation. [FRRouting](https://frrouting.org) est sous licence GPL-2.0 et est installé depuis l’archive d’Ubuntu au démarrage d’une VM ; rien de lui n’est distribué ici.

## À propos

Réalisé par [Mitsuru Technologies](https://mitsurutech.co.uk) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs de VM, des applications web déployées depuis GitHub et des quiz qui se corrigent tout seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même éditeur : [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) et [lab-sample](https://github.com/mitsurutech/lab-sample).
