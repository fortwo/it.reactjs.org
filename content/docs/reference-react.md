---
id: react-api
title: API di Primo Livello di React
layout: docs
category: Reference
permalink: docs/react-api.html
redirect_from:
  - "docs/reference.html"
  - "docs/clone-with-props.html"
  - "docs/top-level-api.html"
  - "docs/top-level-api-ja-JP.html"
  - "docs/top-level-api-ko-KR.html"
  - "docs/top-level-api-zh-CN.html"
---

`React` è il punto d'ingresso della libreria React. Se carichi React da un tag `<script>`, le seguenti API di primo livello sono disponibili nella variabile globale `React`. Se utilizzi ES6 con npm, puoi scrivere `import React from 'react'`. Se utilizzi ES5 con npm, puoi scrivere `var React = require('react')`.

## Panoramica {#overview}

### Componenti {#components}

I componenti React ti consentono di suddividere l'UI in parti indipendenti e riutilizzabili e di ragionare su ciascuna parte in modo isolato dalle altre. I componenti React possono essere definiti creando sottoclassi di `React.Component` o `React.PureComponent`.

 - [`React.Component`](#reactcomponent)
 - [`React.PureComponent`](#reactpurecomponent)

Se non ami le classi ES6, puoi utilizzare il modulo `create-react-class` al loro posto. Leggi [Utilizzare React senza ES6](/docs/react-without-es6.html) per maggiori informazioni.

I componenti React possono anche essere definiti tramite funzioni, che possono essere "wrappate":

- [`React.memo`](#reactmemo)

### Creare Elementi React {#creating-react-elements}

Raccomandiamo di [utilizzare JSX](/docs/introducing-jsx.html) per descrivere l'aspetto che la tua UI dovrebbe avere. Ogni elemento JSX è solamente "zucchero sintattico" sul metodo [`React.createElement()`](#createelement). Se utilizzi JSX, non dovresti mai avere bisogno di invocare direttamente i seguenti metodi.

- [`createElement()`](#createelement)
- [`createFactory()`](#createfactory)

Leggi [Utilizzare React senza JSX](/docs/react-without-jsx.html) per maggiori informazioni.

### Trasformare Gli Elementi {#transforming-elements}

`React` fornisce numerose API per la manipolazione degli elementi:

- [`cloneElement()`](#cloneelement)
- [`isValidElement()`](#isvalidelement)
- [`React.Children`](#reactchildren)

### Frammenti {#fragments}

`React` fornisce anche un componente che può essere utilizzato per renderizzare elementi multipli senza un wrapper.

- [`React.Fragment`](#reactfragment)

### Refs {#refs}

- [`React.createRef`](#reactcreateref)
- [`React.forwardRef`](#reactforwardref)

### Suspense {#suspense}

La Suspense consente ai componenti di "aspettare" qualcosa prima di renderizzare. Ad oggi, la Suspense supporta solamente un caso d'uso: [il caricamento dinamico dei componenti tramite `React.lazy`](/docs/code-splitting.html#reactlazy). In futuro, supporterà anche altri casi d'uso, come il caricamento dei dati.

- [`React.lazy`](#reactlazy)
- [`React.Suspense`](#reactsuspense)

### Hooks {#hooks}

Gli *Hooks* sono una nuova aggiunta in React 16.8. Consentono di utilizzare lo state e altre feature di React senza dichiarare una classe. Gli Hooks hanno una [sezione dedicata della documentazione](/docs/hooks-intro.html) e un riferimento delle API separato:

- [Hooks Di Base](/docs/hooks-reference.html#basic-hooks)
  - [`useState`](/docs/hooks-reference.html#usestate)
  - [`useEffect`](/docs/hooks-reference.html#useeffect)
  - [`useContext`](/docs/hooks-reference.html#usecontext)
- [Hooks Aggiuntivi](/docs/hooks-reference.html#additional-hooks)
  - [`useReducer`](/docs/hooks-reference.html#usereducer)
  - [`useCallback`](/docs/hooks-reference.html#usecallback)
  - [`useMemo`](/docs/hooks-reference.html#usememo)
  - [`useRef`](/docs/hooks-reference.html#useref)
  - [`useImperativeHandle`](/docs/hooks-reference.html#useimperativehandle)
  - [`useLayoutEffect`](/docs/hooks-reference.html#uselayouteffect)
  - [`useDebugValue`](/docs/hooks-reference.html#usedebugvalue)

* * *

## Riferimenti {#reference}

### `React.Component` {#reactcomponent}

`React.Component` è la classe base per definire i componenti React utilizzando le [classi ES6](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes):

```javascript
class Benvenuto extends React.Component {
  render() {
    return <h1>Ciao, {this.props.name}</h1>;
  }
}
```

Leggi i [Riferimenti di React.Component](/docs/react-component.html) per un elenco dei metodi e delle proprietà disponibili nella classe base `React.Component`.

* * *

### `React.PureComponent` {#reactpurecomponent}

`React.PureComponent` è simile a [`React.Component`](#reactcomponent). La differenza è che [`React.Component`](#reactcomponent) non implementa [`shouldComponentUpdate()`](/docs/react-component.html#shouldcomponentupdate) mentre `React.PureComponent` lo implementa con una comparazione "shallow" delle props e dello state.

Se il metodo `render()` del tuo componente React produce sempre lo stesso risultato a partire dagli stessi valori di props e state (ovvero è un componente "puro"), puoi utilizzare `React.PureComponent` per avere un miglioramento della performance in alcuni casi.

> Nota
>
> Il metodo `shouldComponentUpdate()` di `React.PureComponent` effettua solamente una comparazione di tipo "shallow" degli oggetti. Se questi ultimi contengono strutture dati complesse, questo tipo di comparazione potrebbe produrre falsi negativi qualora le differenze siano annidate negli oggetti. Dichiara i tuoi componenti estendendo `PureComponent` solamente quando ti aspetti di avere props o state semplici, oppure utilizza [`forceUpdate()`](/docs/react-component.html#forceupdate) quando sei sicuro che le tue strutture dati siano cambiate in profondità. In alternativa, considera se utilizzare [oggetti immutabili](https://facebook.github.io/immutable-js/) per facilitare la comparazione veloce di strutture dati annidate.
>
> In aggiunta, il metodo `shouldComponentUpdate()` di `React.PureComponent` salta completamente gli aggiornamenti delle props per tutto il sottoalbero del componente. Di conseguenza, se utilizzi `React.PureComponent` assicurati che anche tutti gli eventuali figli del tuo componente siano a loro volta "puri".

* * *

### `React.memo` {#reactmemo}

```javascript
const MioComponente = React.memo(function MioComponente(props) {
  /* renderizza utilizzando le props */
});
```

`React.memo` è un cosiddetto [higher order component](/docs/higher-order-components.html) (componente di ordine superiore). È simile a [`React.PureComponent`](#reactpurecomponent) ma è dedicato ai componenti funzione invece che alle classi componente.

Se il tuo componente funzione renderizza lo stesso risultato a partire dalle stesse props, puoi racchiuderlo in una chiamata a `React.memo` per ottenere un miglioramento della performance in alcuni casi tramite la memoizzazione del risultato. In altre parole, React eviterà di ri-renderizzare il componente, riutilizzando l'ultima renderizzazione.

`React.memo` ha effetto solo sui cambiamenti delle props. Se il tuo componente funzione racchiuso in `React.memo` ha un Hook [`useState`](/docs/hooks-state.html) o [`useContext`](/docs/hooks-reference.html#usecontext) nella sua implementazione, verrà comunque rirenderizzato al cambiamento di state o del context.

Il comportamento predefinito di React è quello di limitarsi a una comparazione shallow degli oggetti complessi contenuti in props. Se vuoi avere un controllo diretto sulla comparazione effettuata, puoi fornire una funzione personalizzata che effettui la comparazione come secondo argomento.

```javascript
function MioComponente(props) {
  /* renderizza utilizzando le props */
}
function sonoUguali(propsPrecedenti, propsSuccessive) {
  /*
  restituisci true se l'invocazione di render con propsSuccessive
  restituirebbe lo stesso risultato dell'invocazione di render con
  propsPrecedenti, altrimenti restituisci false
  */
}
export default React.memo(MioComponente, sonoUguali);
```

Questo metodo esiste solamente come **[strumento per ottimizzare la performance](/docs/optimizing-performance.html).** Non utilizzarlo per "prevenire" la renderizzazione, in quanto farlo può essere causa di bug.

> Nota
>
> A differenza del metodo [`shouldComponentUpdate()`](/docs/react-component.html#shouldcomponentupdate) delle classi componente, la funzione `sonoUguali` restituisce `true` se le props sono uguali e `false` se non lo sono. Il metodo `shouldComponentUpdate` fa esattamente il contrario.

* * *

### `createElement()` {#createelement}

```javascript
React.createElement(
  type,
  [props],
  [...children]
)
```

Crea e restituisce un nuovo [elemento React](/docs/rendering-elements.html) del tipo indicato. L'argomento `type` può essere un nome di tag (ad esempio `'div'` o `'span'`), un tipo di [componente React](/docs/components-and-props.html) (una classe o una funzione), o un tipo di [frammento React](#reactfragment).

Il codice scritto in [JSX](/docs/introducing-jsx.html) verrà convertito in modo da utilizzare `React.createElement()`. Tipicamente, non invocherai direttamente `React.createElement()` se utilizzi JSX. Leggi [React Senza JSX](/docs/react-without-jsx.html) per saperne di più.

* * *

### `cloneElement()` {#cloneelement}

```
React.cloneElement(
  element,
  [props],
  [...children]
)
```

Clona e restituisce un nuovo elemento React utilizzando `element` come punto di partenza. L'elemento risultante riceverà come props lo shallow merge delle props dell'elemento originale e le props passate come argomento. I nuovi children specificati come argomento sostituiranno i children dell'elemento originale. `key` e `ref` dell'elemento originale verranno preservati.

`React.cloneElement()` è pressoché equivalente a:

```js
<element.type {...element.props} {...props}>{children}</element.type>
```

Tuttavia, preserva anche la `ref`. Questo significa che se selezioni un child con una `ref` ad esso, non la "ruberai" accidentalmente dal suo progenitore. Otterrai invece la stessa `ref` assegnata al nuovo componente.

Questa API è stata introdotta per sostituire il metodo deprecato `React.addons.cloneWithProps()`.

* * *

### `createFactory()` {#createfactory}

```javascript
React.createFactory(type)
```

Restituisce una funzione che produce elementi React di un certo tipo. Così come [`React.createElement()`](#createelement), l'argomento `type` può essere un nome di tag (ad esempio `'div'` o `'span'`), un tipo di [componente React](/docs/components-and-props.html) (una classe o una funzione), o un tipo di [frammento React](#reactfragment).

Questo metodo è considerato obsoleto e ti incoraggiamo ad utilizzare al suo posto JSX oppure direttamente `React.createElement()`.

Tipicamente, non invocherai direttamente `React.createFactory()` se utilizzi JSX. Leggi [React Senza JSX](/docs/react-without-jsx.html) per saperne di più.

* * *

### `isValidElement()` {#isvalidelement}

```javascript
React.isValidElement(object)
```

Verifica che l'oggetto sia un elemento React. Restituisce `true` o `false`.

* * *

### `React.Children` {#reactchildren}

`React.Children` fornisce metodi utili per gestire la struttura dati opaca di `this.props.children`.

#### `React.Children.map` {#reactchildrenmap}

```javascript
React.Children.map(children, function[(thisArg)])
```

Invoca la funzione specificata su ciascun figlio diretto contenuto in `children`. Nella funzione, `this` verrà impostato a `thisArg`. Se `children` è un array, verrà attraversato e la funzione verrà invocata per ciascun child nell'array. Se children è `null` o `undefined`, questo metodo restituirà il valore `null` o `undefined`, altrimenti restituirà un array.

> Nota
>
> Se `children` è un `Fragment`, verrà trattato come se fosse un singolo child (e non un array) e quindi non verrà attraversato.

#### `React.Children.forEach` {#reactchildrenforeach}

```javascript
React.Children.forEach(children, function[(thisArg)])
```

Funziona esattamente come [`React.Children.map()`](#reactchildrenmap) ma non restituisce un array.

#### `React.Children.count` {#reactchildrencount}

```javascript
React.Children.count(children)
```

Restituisce il numero totale di componenti in `children`. Se si passasse una funzione ai metodi `map` o `forEach` già descritti, essa verrebbe invocata esattamente quel numero di volte.

#### `React.Children.only` {#reactchildrenonly}

```javascript
React.Children.only(children)
```

Verifica che `children` contenga un solo figlio (un elemento React) e lo restituisce. Negli altri casi questo metodo solleva un errore.

> Nota:
>
>`React.Children.only()` non può essere invocato sul valore restituito da [`React.Children.map()`](#reactchildrenmap) perché è un array e non un elemento React.

#### `React.Children.toArray` {#reactchildrentoarray}

```javascript
React.Children.toArray(children)
```

Restituisce la struttura opaca di `children` come un array appiattito, con una chiave assegnata a ciascun figlio. È utile se vuoi manipolare collezioni di figli nei tuoi metodi render, specialmente se vuoi riordinare `this.props.children` prima di passarlo ai componenti figli o se vuoi passarne solo una parte.

> Nota:
>
> `React.Children.toArray()` modifica le chiavi per conservare la semantica degli array annidati quando appiattisce liste di figli. In altre parole, `toArray` aggiunge un prefisso in ciascuna chiave nell'array restituito in modo che la chiave di ciascun elemento sia riferita all'array di input che conteneva quell'elemento.

* * *

### `React.Fragment` {#reactfragment}

Il componente `React.Fragment` ti consente di restituire elementi multipli come risultato di un metodo `render()` senza dover creare un element DOM aggiuntivo per contenerli:

```javascript
render() {
  return (
    <React.Fragment>
      Un po' di testo.
      <h2>Un'intestazione</h2>
    </React.Fragment>
  );
}
```

Puoi utilizzarlo anche tramite la notazione compatta `<></>`. Per saperne di più, leggi [React v16.2.0: Supporto ai Frammenti Migliorato](/blog/2017/11/28/react-v16.2.0-fragment-support.html).


### `React.createRef` {#reactcreateref}

`React.createRef` crea una [ref](/docs/refs-and-the-dom.html) che può essere assegnata agli elementi React tramite l'attributo ref.

`embed:16-3-release-blog-post/create-ref-example.js`

### `React.forwardRef` {#reactforwardref}

`React.forwardRef` crea un componente React che inoltra l'attributo [ref](/docs/refs-and-the-dom.html) che riceve a un altro componente più in basso nell'albero. Questa tecnica non è molto comune, ma è particolarmente utile in due scenari:

* [Inoltrare le refs ai componenti del DOM](/docs/forwarding-refs.html#forwarding-refs-to-dom-components)
* [Inoltrare le refs nei componenti di ordine superiore](/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)

`React.forwardRef` accetta una funzione di renderizzazione come argomento. React invocherà quella funzione passando `props` e `ref` come argomenti. La funzione dovrebbe restituire un nodo React.

`embed:reference-react-forward-ref.js`

Nell'esempio in alto, React passa una `ref` all'elemento `<BottoneFigo ref={ref}>` come secondo argomento alla funzione di renderizzazione all'interno della chiamata a `React.forwardRef`. Questa funzione di renderizzazione a sua volta passa la `ref` all'elemento `<button ref={ref}>`.

Il risultato è che, dopo che React ha connesso la ref, `ref.current` punterà direttamente all'istanza dell'elemento del DOM `<button>`.

Per maggiori informazioni, leggi [inoltrare le refs](/docs/forwarding-refs.html).

### `React.lazy` {#reactlazy}

`React.lazy()` ti consente di definire un componente che è caricato dinamicamente ("lazy" significa letteralmente "pigro"). Questo aiuta a ridurre le dimensioni del pacchetto della tua applicazione ritardando il caricamento dei componenti che non sono utilizzati durante la renderizzazione iniziale.

Puoi imparare come utilizzare questo metodo nella nostra [documentazione sulla suddivisione del codice](/docs/code-splitting.html#reactlazy). Potresti voler anche leggere [questo articolo](https://medium.com/@pomber/lazy-loading-and-preloading-components-in-react-16-6-804de091c82d) in cui si spiega con ancora maggior dettaglio come utilizzarlo.

```js
// Questo componente è caricato dinamicamente
const UnComponente = React.lazy(() => import('./UnComponente'));
```

Nota che la renderizzazione dei componenti `lazy` richiede che sia presente un componente `<React.Suspense>` più in alto nell'albero di renderizzazione. Questo è il modo in cui puoi specificare un indicatore di caricamento.

> **Nota**
>
> Utilizzare `React.lazy` con l'importazione dinamica richiede che l'ambiente JavaScript supporti le Promises. Questo significa che è necessario utilizzare un polyfill su IE11 e versioni precedenti.

### `React.Suspense` {#reactsuspense}

`React.Suspense` ti consente di specificare un indicatore di caricamento nel caso in cui alcuni componenti dell'albero sotto di esso non siano ancora pronti per la renderizzazione. Ad oggi, il caricamento lazy dei componenti è **l'unico** caso d'uso supportato da `<React.Suspense>`:

```js
// Questo componente viene caricato dinamicamente
const UnAltroComponente = React.lazy(() => import('./UnAltroComponente'));

function MioComponente() {
  return (
    // Mostra <Clessidra> finché UnAltroComponente non è pronto
    <React.Suspense fallback={<Clessidra />}>
      <div>
        <UnAltroComponente />
      </div>
    </React.Suspense>
  );
}
```

Tutto ciò è documentato nella nostra [guida per la separazione del codice](/docs/code-splitting.html#reactlazy). Nota che i componenti `lazy` possono anche essere molto in profondità nell'albero che fa capo a `Suspense` -- non è necessario usarlo per racchiuderli uno per uno. La pratica consigliata è quella di inserire `<Suspense>` nel punto in cui vuoi visualizzare un indicatore di caricamento, e di utilizzare `lazy()` dovunque vuoi effettuare la separazione del codice.

Anche se al momento non sono supportati, in futuro abbiamo in programma di utilizzare `Suspense` per gestire altri scenari, ad esempio il caricamento di dati. Puoi trovare maggiori informazioni al riguardo nella [nostra roadmap](/blog/2018/11/27/react-16-roadmap.html).

>Nota:
>
>`React.lazy()` e `<React.Suspense>` non sono ancora supportati da `ReactDOMServer`. Questa è una limitazione nota che verrà risolta in futuro.
