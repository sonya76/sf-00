# Symfony

- Créer un nouveau projet **Symfony**

`symfony new ceppic --webapp`

- Créer un controller `Home` qui prend comme route `/`

- Mettre en place `bootstrap css`

- Ajouter au fichier `base.html.twig` la structure de page suivante :
  
  - Header avec une navbar
  - Main : contenu site site web
  - Footer : mettre un texrte dans ce footer `Site crée par xxx - 2022`

- Lancer `mysql`avec `xampp`

- Dans votre projet `ceppic` copier le fichier `.env` en `.env.local`

- dans `.env.local` commenter la ligne qui commence par `DATABASE_URL="postgresql...`

- Ajouter dans `.env.local` decommenter la ligne `DATABASE_URL="mysql...` et la completer par les informations suivantes `DATABASE_URL="mysql://root@127.0.0.1:3306/ceppic"`

La base de donnée sera `ceppic`

- Créer la base de donnée avec la commande suivante : `symfony console doctrine:database:create` ou `symfony console d:d:c`

- Créer une table dans notre base de données `ceppic` : 

`symfony console make:entity` vous l'appelerez `article`

- On ajoute des champs à notre table `article` :

  - `titre` avec comme **type** `string` de longeur `255` et le champ ne peut etre null `no`
  - `description` avec comme **type** `text`

- On crée les requêtes avec `symfony console make:migration` , elles vont se trouver dans `/migrations`

- Excecuter ses requêtes : `symfony console doctrine:migrations:migrate`

- Verifier avec `phpmyadmin` que la base de donnée et que la table sont bien presentes

- Répeter la même operation pour créer une `entity` qu'on nommera `categorie` avec un seul `titre` qui aura les mêmes caracteristiques que le `titre` de `article`

**Attention maintenant c'est l'étape ou on peut se rater !**

- On va créer une **relation** entre les 2 tables `article` et `categorie`

  - `symfony console make:entity`

  - `Class name of the entity to create or update :` : mettre `article`

  - `New property name (press <return> to stop adding fields):` : mettre `categorie`

  - `Field type (enter ? to see all types) [string]:` : mettre `relation` 

  - `What class should this entity be related to?:` : mettre `Categorie` **attention à la casse** c'est bien un c **majuscule**

  - `Relation type? [ManyToOne, OneToMany, ManyToMany, OneToOne]:` :  choisir `ManyToOne`

  - `Is the Produit.categorie property allowed to be null (nullable)? (yes/no) [yes]:` : mettre `no`

  - Ensuite valide tout par default pour sortir

- On fait la migration :

 - `symfony console make:migration`
 - `symfony console doctrine:migrations:migrate`

 - On rentre des données dans notre tabble `categories`

   - `symfony console doctrine:query:sql "INSERT INTO categorie (titre) VALUES ('informatique')"`
   - `symfony console doctrine:query:sql "INSERT INTO categorie (titre) VALUES ('gestion')"`
   - `symfony console doctrine:query:sql "INSERT INTO categorie (titre) VALUES ('securite')"`

- Pour inserer des articles nous allons le faire un fichier de `migration` : `symfony console doctrine:migrations:generate`

- Modifier le fichier crée dans `/migrations` avec :

```
public function up(Schema $schema): void
    {
        $this->addSql("INSERT INTO article (titre, description, categorie_id) VALUES ('concepteur, developpeur informatique', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco', 1)");
        $this->addSql("INSERT INTO article (titre, description, categorie_id) VALUES ('marketing digital et gestion de sites e-commerce', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco', 2)");
        $this->addSql("INSERT INTO article (titre, description, categorie_id) VALUES ('agent de sûrete et de securitee privee', 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco', 3)");
    }
```

- Effectuer la migration avec le bon identifiant de la migration dans le fichier `Versionxxxxx` : 
   - `symfony console doctrine:migrations:execute 'DoctrineMigrations\Versionxxxxx'`
   - `symfony console doctrine:migrations:generate`

- On liste tous les articles dans notre controller `Home` :

```
 <?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

use App\Repository\ArticleRepository;

class HomeController extends AbstractController
{
    #[Route('/', name: 'home')]
    public function index(ArticleRepository $repo): Response
    {
       
        $listeArticle = $repo->findAll();

        return $this->render('home/index.html.twig', [
            'liste_article' => $listeArticle,
        ]);
    }
}
```

- On va lister les articles dans la vue `/templates/home/index.html.twig`

```
 {% extends 'base.html.twig' %}

{% block body %}

<h2>Liste des articles</h2>

<ul>

    {% for article in liste_article %}

        <li>{{ article.titre }}</li>

    {% endfor %}

</ul>
{% endblock %} 
```

Adapter cette presentation des articles en les mettant dans des **cards** avec **bootstrap**.

- Ajouter une nouvelle route dans `/src/Controller/HomeController.php`

```
#[Route('/article/{id}', name: 'route_article')]
    public function article(int $id, ArticleRepository $repo): Response 
    {

        $article = $repo->find($id);

        return $this->render('home/article.html.twig', [
            'article' => $article, 
        ]);
    }
```

- Dans `templates/home/article.html.twig` on affiche le detail de chaque article :

```
{% extends 'base.html.twig' %}

{% block body %}
<ul>
<li>Titre : {{ article.titre }} </li>
<li>Description : {{ article.description }} </li>
<li>Catégorie : {{ article.categorie.titre }} </li>
</ul>
{% endblock %}
```

- Retravailler cette presentation en vous aidant de **bootstrap**

- On va faire en sorte d'ajouter un lien sur chaque pour avoir son detail dans `/templates/home/index.html.twig`

```
 {% extends 'base.html.twig' %}

{% block body %}

<h2>Liste des articles</h2>

<ul>

    {% for article in liste_article %}

        <li>{{ article.titre }}</li>
        <li>{{ article.titre }} - <a href="{{ path ('route_article', {'id': article.id}) }}">lien</a></li>

    {% endfor %}

</ul>
{% endblock %} 
```


## Git

- Faite un **fork** du dépôt 
- Travailler dans une branche **develop**
- Faire des **commits atomiques**
- Tous vos **commits** doivent commencer par une majuscule
- Les branches **feature** doivent se nommer **feature/nom_de_la_feature**
- Faites des **pull request**

## Questions

Si vous avez des questions ouvrer des **issues** sur ce dépôt et non sur votre **fork** !.

## Attendu

- Faite bien un commit par action effectué 
- Si vous avez des problemes de connection à la base de donnée , la créer directement dans `phpmyadmin`
