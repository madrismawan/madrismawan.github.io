---
title: "Pokédex dashboard use Svelte"
layout: post
date: 2023-03-13 20:10
tag: [frontend, svelte, design pattern, state management  ]
image: https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/International_Pok%C3%A9mon_logo.svg/1280px-International_Pok%C3%A9mon_logo.svg.png
headerImage: true
projects: true
hidden: true
description: "Simple project Frontend use Svelte. This web only view list Pokemon use PokeAPI."
category: project
author: maderismawan
externalLink: false
---

![Screenshot](https://madrismawan.github.io/assets/images/project/pokedex/dashboard-web.png)

I init Project Pokedex in 2023 because I was interested in trying out a trending framework in the 2022s,Framework Svelte, which offers highly efficient and lightweight code with a mechanism of compiling JS without virtual DOM/library runtime. The syntax in Svelte is relatively easy to learn, similar to Vue syntax. as it is a relatively new framework with limited library choices. [Svelte Pokédex](https://pokemon-web-two.vercel.app/)

## Library Used
* Svelte Store - (State Management)
* Tailwind - (CSS)
* Animate.css - (Animation)
* Axios - (HTTP Clients)

## Folder Structure
Trying to implement Design Pattern DDD with separate by layer application, stores, and domain. This folder structure use to build the project

{% highlight raw %}
└── 📁src
    └── app.css
    └── app.d.ts
    └── app.html
    └── 📁application
        └── pokemon.ts
    └── 📁componets
        └── inputField.svelte
        └── 📁pokemon
            └── modal.svelte
            └── pokeCard.svelte
        └── search.svelte
        └── skeleton.svelte
        └── socialMedia.svelte
    └── 📁domain
        └── 📁entities
            └── DetailPokemon.ts
            └── Pokemon.ts
            └── PokemonSpecies.ts
        └── 📁repositories
            └── PokemonRepository.ts
    └── 📁layouts
        └── +footer.svelte
        └── +header.svelte
    └── 📁lib
        └── leftPadNumber.ts
        └── pokemonType.ts
        └── socialMedia.ts
    └── 📁routes
        └── +layout.svelte
        └── +page.svelte
        └── styles.css
    └── 📁services
        └── apiService.ts
        └── httpService.ts
    └── 📁stores
        └── 📁models
            └── Pokemon.ts
        └── 📁moduls
            └── pokemon.ts
{% endhighlight %}

Thanks for reading, to explore the project you can visit in [PokemonWeb](https://github.com/madrismawan/PokemonWeb).



