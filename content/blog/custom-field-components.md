---
title: Ceci est un Article
date: '2020-01-20T07:00:00.000Z'
author: Kendall Strautman
draft: false
consumes:
  - file: /packages/next-tinacms-json/src/use-local-json-form.ts
    details: Example shows useLocalJsonForm in use
  - file: /packages/next-tinacms-json/src/use-json-form.ts
    details: Example shows useLocalJsonForm in use
  - file: /packages/@tinacms/forms/src/form.ts
    details: Depends on the Field interface
  - file: /packages/@tinacms/form-builder/src/field-plugin.tsx
    details: Depends on the FieldPlugin interface
---
Les champs de formulaire sont le pain et le beurre de tout CMS. Alors que Tina fournit une solide collection de champs «prêts à l'emploi», vous pouvez également créer les vôtres. Cet article vous montrera les concepts de base de la façon de créer des composants de champ personnalisés et de les utiliser dans la barre latérale de Tina.

**Prérequis 👩‍🏫**

Tout au long de la publication, je ferai référence à quelques concepts de base de TinaCMS tels que les [formulaires](https://tinacms.org/docs/forms) , la barre latérale et les [champs](https://tinacms.org/docs/fields) . Il sera utile d'avoir une connaissance pratique de la base du fonctionnement de [**TinaCMS**](https://tinacms.org/docs/getting-started/how-tina-works) avant de lire. N'hésitez pas à vous référer à la [documentation](https://tinacms.org/docs/getting-started/introduction) ou à lire un article sur l'utilisation de Tina avec [Gatsby](https://www.gatsbyjs.org/blog/2019-12-20-integrate-tinacms-with-your-gatsby-website/) ou [Next.js](https://tinacms.org/blog/using-tinacms-with-nextjs/) pour vous familiariser.

## Pourquoi voudriez-vous créer un champ personnalisé?

Tina devait être entièrement personnalisable et extensible. La création de **champs personnalisés peut fournir un contrôle précis** sur la configuration et le style de la barre latérale, ainsi que la mise en œuvre d'une fonctionnalité de champ unique.

![saturation-custom-field-gif](/gif/saturate-custom-field.gif)

> Envie d'aller de l'avant? N'hésitez pas à consulter une [**version finie**](https://github.com/kendallstrautman/llama-filters)                                                                                                                                                                                                                                 **du champ de saisie de la plage personnalisée** vu dans le gif ci-dessus, ou jetez un œil à un [plugin de champ ](https://github.com/tinacms/tina-starter-grande/blob/master/src/fields/authors.js)_[Auteurs](https://github.com/tinacms/tina-starter-grande/blob/master/src/fields/authors.js)_ plus complexe dans le repo Tina Grande.

## Deux méthodes - Commençons simplement

Il existe deux façons d'ajouter [des champs personnalisés](https://tinacms.org/docs/fields/custom-fields) à Tina. La première approche consiste à _définir un composant Réagir et à passer dans la `component`propriété_ d'une définition de champ. L'équipe Tina y fait référence en tant que **composant de terrain en ligne.** Cette option est plus simple; ce sera la méthode de mise au point dans ce post.

La deuxième approche consiste à définir un composant personnalisé, puis à enregistrer ce composant en tant que [plugin de champ](https://tinacms.org/docs/fields/custom-fields#2-creating-field-plugins) auprès du CMS. Tous les [champs principaux](https://tinacms.org/docs/fields) fournis par Tina sont utilisés comme plugins.

Il existe certains avantages à créer un plugin par rapport à un champ en ligne - les principaux points étant la réutilisabilité et l'accès aux fonctions supplémentaires pour l'analyse, la validation, etc. Mais **pour les cas plus simples** , lorsque vous avez besoin d'un champ personnalisé sous une seule forme ou que vous n'avez pas besoin de validation de besoin, un composant de champ en ligne fera très bien l'affaire 👌.

## Création d'un champ en ligne personnalisé

Imaginons qu'un [formulaire Tina soit](https://tinacms.org/docs/forms) configuré pour une page _À propos de moi_ :

> _Remarque:_ Les exemples ci-dessous offrent une référence à une configuration Next.js, mais cette approche peut également être appliquée à Gatsby.

```js
const formOptions = {
  label: 'About Me Page',
  fields: [
    {
      label: 'Name',
      name: 'name',
      component: 'text',
    },
    {
      label: 'Hometown',
      name: 'hometown',
      component: 'text',
    },
    {
      label: 'Color',
      name: 'background_color',
      description: 'Background Color',
      component: 'color',
    },
  ],
}
```

Nous pourrions ajouter un composant de champ en ligne personnalisé pour organiser davantage la barre latérale:

```js
const formOptions = {
  label: 'Info Page',
  fields: [
    {
      label: 'Name',
      name: 'name',
      component: 'text',
    },
    {
      label: 'Hometown',
      name: 'hometown',
      component: 'text',
    },
    // This is our custom inline field 👀
    {
      name: '_',
      component: () => <h4>Page Styles</h4>,
    },
    {
      label: 'Color',
      name: 'background_color',
      description: 'Background Color',
      component: 'color',
    },
  ],
}
```

_Assez cool hein?_ 🤩

Remarquez commentez dans tous les autres objets de champ, la `component`propriété fait référence à un plugin de champ Tina, alors **que notre champ en ligne personnalisé, nous transmettons un composant React.**

![Custom Inline Field In Sidbar](/img/blog/custom-field-inline.png)

Maintenant, cet exemple de composant est super simple - une étiquette glorifiée. Ce type de composant peut être utile pour l'organisateur ou personnaliser la barre latérale, mais _nous pouvons aller plus loin et passer dans des domaines plus complexes_ .

## Curseur de plage personnalisé 🎨

Supposons que nous avions une image sur la page _À propos de moi_ et que nous voulions pouvoir contrôler certains filtres [CSS](https://css-tricks.com/almanac/properties/f/filter/) sur cette image. Le stylo ci-dessous montre tous les filtres CSS avec nous devons jouer.

<iframe height = "450" ​​style = "width: 100%;" scrolling = "no" title = "Filtres CSS + Un Springer Spaniel" src = "https://codepen.io/kendallstrautman/embed/WNbzLJZ?height=265&theme-id=default&default-tab=css,result" frameborder = "no "allowtransparency =" true "allowfullscreen =" true "> Voir le Pen <a href='https://codepen.io/kendallstrautman/pen/WNbzLJZ'> Filtres CSS + Un Springer Spaniel </a> par Kendall strautman (< un href = 'https: //codepen.io/kendallstrautman'> @kendallstrautman </a>) sur <a href='https://codepen.io'> CodePen </a>. </iframe>

Nous pouvons créer un champ de saisie personnalisé pour fournir un contrôle d'édition sur ces filtres visuels. **Créons un champ personnalisé qui contrôle la saturation de l'image.**

> **Astuce:** La saturation en photographie se rapporte à l' _intensité des couleurs particulières dans une image_ . Une image très saturée serait très lumineuse, avec des couleurs bordant le néon. Une image avec une faible saturation apparaîtra en sourdine et grise.

### 1. Créez le composant de champ de saisie

Pour créer un champ de saisie personnalisé, nous devons créer un **composant Réagissez qui prend en entrée et rencontré à jour les données lorsque l'entrée est modifiée** . Pour cet exemple, nous allons créer un [champ de saisie de plage](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/range) qui gère l'état de la valeur de saturation et le jour à chaque fois que le contrôle de la plage est glissé.

```jsx
// An example of a custom range field component
function RangeInput(props) {
  return (
    <>
      <div>
        <label htmlFor="saturation">Image Saturation</label>
      </div>
      <div>
        <input
          name="saturation"
          id="saturation"
          type="range"
          min="0"
          max="10"
          step=".1"
          /*
           ** This special input
           ** object sets essential
           ** input props: value,
           ** onChange, onFocus etc.
           */
          {...props.input}
        />
      </div>
    </>
  )
}
```

#### 👽 Regardez de plus près - Accessoires:

Cette ligne Remarquez `{...props.input}`,. Vous vous demandez peut-être d'où vient cet objet magique avec tous les accessoires nécessaires?

Lorsque le champ personnalisé est enregistré auprès de Tina, cet **objet d'entrée** est transmis comme accessoire au champ. Contient des CET nécéssaires et callbacks l'objet trabalho plat principale Données Verser Verser correctement: [`value`](https://final-form.org/docs/react-final-form/types/FieldRenderProps#inputvalue), [`name`](https://final-form.org/docs/react-final-form/types/FieldRenderProps#inputname), [`onChange`](https://final-form.org/docs/react-final-form/types/FieldRenderProps#inputonchange), [`onFocus`](https://final-form.org/docs/react-final-form/types/FieldRenderProps#inputonfocus), [`onBlur`](https://final-form.org/docs/react-final-form/types/FieldRenderProps#inputonblur).

> Si votre composant personnalisé n'est pas un [élément d'entrée HTML](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input) standard, vous devez passer avec les accessoires d'entrée nécessaires, au lieu utilisé l' [opérateur d'étalement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) .

**Tous les accessoires** passés au composant de champ sont:

* `field`- Une référence à la [définition](https://tinacms.org/docs/fields) du [champ](https://tinacms.org/docs/fields) .
* `input`- L'objet avec des données et des rappels pour le champ pour définir et mettre à jour les données. _Décrit ci-dessus_ ☝️.
* `meta`- Cela fournit des [métadonnées](https://final-form.org/docs/react-final-form/types/FieldRenderProps#metaactive) sur l'état du champ.
* `tinaForm` - Une référence au formulaire où ce champ est enregistré.

La [documentation réagira-forme finale](https://final-form.org/docs/react-final-form/api/Field#3-connect-the-callbacks-to-your-input) décrit le `input`et `meta`incroyablement bien accessoires. Lors de la création de champs personnalisés, vous aurez divers accès aux accessoires `field`et `input`.

#### Où le champ personnalisé doit-il vivre?

Comme nous avons vu vu dans le premier exemple, nous pouvons passer le composant de champ personnalisé directement via la `component`propriété - `component: () => <p>Hi<p>`. Mais lorsque nous voulons des champs plus complexes, nous voudrons très probablement extraire le champ dans sa propre fonction.

Dans l'exemple ci-dessus, `RangeInput`pourrait être défini à côté du `AboutMe`composant où la forme Tina est configurée:

```jsx
/*
** Custom field defined alongside
** component using a Tina Form
*/
import { useLocalJsonForm, JsonFile } from "next-tinacms-json";

export default function AboutMe(props) {
  // Tina Form config
  const [data] = useLocalJsonForm(props.data, formOptions)
  return (
    //...
  )
}

function RangeInput(props) {
  //...
}

const formOptions = {
  /*
  ** RangeInput will be referenced
  ** in the custom field definition
  */
}

AboutMe.getInitialProps = async function() {
  //...
}
```

Il peut également être défini dans son propre fichier et importé dans le fichier où les options du formulaire sont configurées:

```jsx
/*
** Custom field definition kept in
** separate file and imported
*/
import { useLocalJsonForm, JsonFile } from "next-tinacms-json";
import RangeInput from '../components/RangeInput';

export default function AboutMe(props) {
  // Tina Form config
  const [data] = useLocalJsonForm(props.data, formOptions)
  return (
    //...
  )
}

const formOptions = {
  /*
  ** RangeInput will be referenced
  ** in the custom field definition
  */
}

AboutMe.getInitialProps = async function() {
  //...
}
```

Comme pour beaucoup de choses en développement, la réponse **dépend de votre cas d'utilisation** 😉. N'hésitez pas à référencer ce [dépôt de démonstration](https://github.com/kendallstrautman/llama-filters/blob/master/pages/Index.js) pour voir un exemple de structure de travail pour Next.js.

### 2. Ajoutez la valeur aux données source

Maintenant que le champ de saisie personnalisé est défini, nous devons ajouter la `image_saturation`valeur à nos données source. Les données source peuvent être un fichier Markdown ou JSON. Si vous avez déjà configuré un formulaire Tina, il doit être lié à une source de données, alors dirigez-vous vers ce fichier.

Pour notre exemple, supposons que nous ayons un fichier JSON local appelé `about.json`. Ce fichier contient les données utilisées dans la page _À propos de moi_ . Nous pouvons y ajouter de la `image_saturation`valeur.

La valeur peut être n'importe quel nombre entier ou virgule flottante qui existe entre la plage définie dans notre `RangeInput`composant - 0 à 10, avec un pas de 0,1 (ce qui signifie que chaque \`\` pas de diapositive '' de la plage augmente ou diminue la valeur de 0,1). En tant que valeur de saturation, **zéro serait totalement en niveaux de gris** ou sans couleur, nous pouvions donc remplir quelque chose comme 3 pour obtenir un aspect plus «normal».

```JSON
// Example About Me Page source data --> about.json
{
 “name”: “Koba Weasley”,
 “hometown”: “Bend, Oregon”,
 "background_color": "#B1BCBC",
 "image_saturation": 3,
}
```

> Si vous utilisez Gatsby, vous **devez mettre à jour votre requête GraphQL** pour obtenir ces nouvelles données. Ajoutez le `image_saturation`champ à votre requête.

Nous avons donc maintenant une valeur source qui peut être connectée au champ de saisie personnalisée. De cette façon, **Tina peut mettre à jour la valeur dans le fichier source** en synchronisation avec les modifications récupérées par le `RangeInput`composant.

### 3. Ajoutez le champ personnalisé à un formulaire Tina

Que diriez-vous de câbler ce champ personnalisé à Tina? 🎊

Dans cette étape, nous devons créer la définition de champ personnalisé et transmettre le `RangeInput`composant en ligne. Nous reviendrons à nos [options de formulaire de](https://tinacms.org/docs/gatsby/json#customizing-json-forms) page _À propos de moi_ :

```jsx
const formOptions = {
  label: 'About Me Page',
  fields: [
    {
      label: 'Name',
      name: 'name',
      component: 'text',
    },
    {
      label: 'Hometown',
      name: 'hometown',
      component: 'text',
    },
    {
      name: '',
      component: () => <h4>Page Styles</h4>,
    },
    // Pass `RangeInput` to the `component` property
    {
      label: 'Image Saturation',
      name: 'image_saturation',
      component: RangeInput,
    },
    {
      label: 'Color',
      name: 'background_color',
      description: 'Background Color',
      component: 'color',
    },
  ],
}
```

Démarrez le serveur de développement et vous devriez voir le `RangeInput`champ personnalisé dans la barre latérale. Si vous faites glisser, vous devriez voir la valeur se mettre à jour dans `about.json`.

### 4. Définissez dynamiquement le filtre CSS

Si tout s'est bien passé, notre champ de saisie personnalisé devrait être câblé, mais il y a une dernière chose à faire. Nous n'avons pas connecté la _valeur de saturation_ avec un filtre CSS pour voir réellement un effet sur l'image.

Pour ce faire, vous devez utiliser un cadre _[CSS-dans-JS](https://css-tricks.com/bridging-the-gap-between-css-and-javascript-css-in-js/)_ afin que nous puissions mettre à jour dynamiquement les valeurs de filtre via les accessoires de composant. Si vous utilisez Next.js, `styled-jsx`fonctionne immédiatement et est assez fantastique. Voici un exemple de la _valeur de saturation_ connectée au filtre CSS avec `styled-jsx`:

```jsx
/*
 **  Example component for the
 **  About Me page in Next.js
 */
import { useLocalJsonForm } from 'next-tinacms-json'

function AboutMe(props) {
  const [data] = useLocalJsonForm(props.data, formOptions)
  return (
    <Layout bgColor={data.background_color}>
      <section>
        <h1>Hi 👩‍🎤 my name is {data.name}</h1>
        <p>Currently gallivanting around {data.hometown}</p>
        {/* This is the image that will get the treatment */}
        <img
          alt="random-unsplash"
          src="https://source.unsplash.com/random/800x600"
        />
      </section>
      {/* Pass in the image_saturation value */}
      <style jsx>{`
        img {
          filter: saturate(${data.image_saturation});
        }
      `}</style>
    </Layout>
  )
}
```

Quelques autres exemples de frameworks _CSS-in-JS_ impressionnants sont les [composants de style](https://www.styled-components.com/) et [emotion.js](https://emotion.sh/docs/introduction) . Notez que l'implémentation ci-dessus pour ces cadres alternatifs sera légèrement différente.

### Prochaines étapes

Une bonne prochaine étape serait d' _ajouter des styles au `RangeInput`composant personnalisé_ . Vous pouvez utiliser [`@tinacms/styles`](https://tinacms.org/docs/fields/custom-fields#using-tina-styles)pour s'adapter à l'ambiance des autres champs Tina ✌️. Vous pouvez déchaîner et pimenter la barre latérale à votre façon 🤠.

Si nous voulions réutiliser ce composant sur l'ensemble du site, **nous pourrions aller plus loin et en faire un**                                                                                                                                                                                                                       [**plugin de terrain**](https://tinacms.org/docs/fields/custom-fields#2-creating-field-plugins) . Restez à l'écoute pour un article de suivi qui plonge dans la création de plugins de terrain personnalisés, ou passez par les [documents](https://tinacms.org/docs/fields/custom-fields#2-creating-field-plugins) pour prendre une longueur d'avance.

> N'hésitez pas à consulter une [**version finale**](https://github.com/kendallstrautman/llama-filters)                                                                                                                                                                                                                       **de ce champ de saisie de plage personnalisée** ou à découvrir un [plugin de champ ](https://github.com/tinacms/tina-starter-grande/blob/master/src/fields/authors.js)_[Auteurs](https://github.com/tinacms/tina-starter-grande/blob/master/src/fields/authors.js)_ plus complexe dans le référentiel Tina Grande.

### À emporter 🕺🏻

Faire des composants de terrain personnalisés pour TinaCMS est incroyablement excitant! Avec un peu de chance, ce post a permis à vos créatifs d'activer les nombreuses variables à bricoler dans l'expérience d'édition de contenu.

Je pense que le plus grand enseignement de cette courte exploration des champs personnalisés est que **vous pouvez mettre n'importe quel composant Réagissez dans la barre latérale** . Cette flexibilité est très puissante; il vous ouvre la porte pour personnaliser les contrôles d'édition d'un projet en fonction de ses besoins uniques. Et bien que la création de composants personnalisés ne soit pas toujours nécessaire, le simple fait de savoir que c'est une option est rassurant, voire inspirant.