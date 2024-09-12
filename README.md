# Coucou !

Après la création de la database en `MySQL` nommée `coucousymfonyg2`,

Dans le `.env.local` on met le chemin vers la DB (en commentant le lien vers `postgresql`)

```env
DATABASE_URL="mysql://root:@127.0.0.1:3306/coucousymfonyg2?serverVersion=8.0.31&charset=utf8mb4"
```

## Création d'entités

Nous allons créer des entités.

    php bin/console make:entity Post

    php bin/console make:entity Section

    php bin/console make:entity Tag

    php bin/console make:entity Comment

Nous avons 4 entités vides (mise à part l'id) et leurs Repository ("Managers" complémentaires à `Doctrine`).

Comme on va utiliser `MySQL`, on va modifier l'id en `unsigned`

```php
#[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

### modifié dans tous les Entities en
#[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column(
        options:
            [
                'unsigned' => true,
            ]
    )]
    private ?int $id = null;
```

### Migration

    php bin/console make:migration

Création d'un fichier dans `migrations`

Migration:

    php bin/console doctrine:migration:migrate
    ou
    php bin/console d:m:m

### Modification de Post

    php bin/console make:entity Post

```php
<?php

namespace App\Entity;

use App\Repository\PostRepository;
use Doctrine\DBAL\Types\Types;
use Doctrine\ORM\Mapping as ORM;

#[ORM\Entity(repositoryClass: PostRepository::class)]
class Post
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column(
        # on veut que l'ID soit 'unsigned'
        options:
        [
            'unsigned' => true,
        ]
    )]
    private ?int $id = null;

    #[ORM\Column(length: 160)]
    private ?string $postTitle = null;

    #[ORM\Column(type: Types::TEXT)]
    private ?string $postDescription = null;

    #[ORM\Column(
        type: Types::DATETIME_MUTABLE,
        # valeur par défaut CURRENT_TIMESTAMP
        options: [
            'default' => 'CURRENT_TIMESTAMP',
        ]
    )]
    private ?\DateTimeInterface $postDateCreated = null;

    #[ORM\Column(
        # il peut être null
        type: Types::DATETIME_MUTABLE,
        nullable: true)]
    private ?\DateTimeInterface $postDatePublished = null;

    #[ORM\Column]
    private ?bool $postPublished = null;

    ### getters and setters

```

Si on vérifie qu'il y a des différences entre la DB et les entités :

    php bin/console doctrine:migrations:diff

Doctrine se permet de créer une migration si nécessaire !

Exécution de la migration

    php bin/console d:m:m

### Modification de Section

    php bin/console make:entity Section

```php
<?php



#[ORM\Entity(repositoryClass: SectionRepository::class)]
class Section
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column(
        options:
        [
            'unsigned' => true,
        ]
    )]
    private ?int $id = null;

    #[ORM\Column(length: 160)]
    private ?string $sectionTitle = null;

    #[ORM\Column(length: 600, nullable: true)]
    private ?string $sectionDescription = null;

  
}
```

### Modification de Tag

    php bin/console make:entity Tag

```php
<?php



#[ORM\Entity(repositoryClass: TagRepository::class)]
class Tag
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column(
        options:
        [
            'unsigned' => true,
        ]
    )]
    private ?int $id = null;

    #[ORM\Column(
        # ce sera un champ unique
        length: 60,
        unique: true,
    )]
    private ?string $tagName = null;

   
}
```

### Modification de Comment

    php bin/console make:entity Comment

```php
<?php
#[ORM\Entity(repositoryClass: CommentRepository::class)]
class Comment
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column(
        options:
            [
                'unsigned' => true,
            ]
    )]
    private ?int $id = null;

    #[ORM\Column(length: 2500)]
    private ?string $commentMessage = null;

    #[ORM\Column(
        type: Types::DATETIME_MUTABLE,
        options: [
            'default' => 'CURRENT_TIMESTAMP',
        ]
    )]
    private ?\DateTimeInterface $commentDateCreated = null;
```

On fait la migration

    php bin/console make:migration

    php bin/console d:m:m


### Les relations

On va commencer par la relation `ManyToMany` depuis `Post` vers `Section`

    php bin/console make:entity Post

On choisit `sections` -> `ManyToMany` -> `Section` -> `yes` -> `posts`

#### Pour créer un tag

    git tag -a tagsansespcev0 -m"description"

Pour l'envoyer

    git push origin tagsansespcev0

### CRUD de Post


Dans la branche `coucou`

    php bin/console make:crud Post
    > AdminPostController
    > test -> yes
### Création d'un menu

Dans `base.html.twig`, car les fichiers automatiques en héritent tous

```twig
{# templates/base.html.twig #}
{# ... #}
    <body>
        <nav>
            <a href="{{ path('homepage_coucou') }}">Accueil</a>
            <a href="{{ path('app_admin_post_index') }}">Post CRUD</a>
        </nav>
        {% block body %}{% endblock %}
    </body>
{# ... #}   
```