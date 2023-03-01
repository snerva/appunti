# API


## AJAX

- È una tecnologia JavaScript e XML.
- Ascincrona poichè permette di inviare/ricevere dati a/da server senza ricaricare la pagina.

## Axios

- Libreria esterna che consente di eseguire chiamate asincrone.

```js

import { axios } from 'axios';

const { createApp } = Vue;

createApp({
    data() {
        return {
            my_data: null,
            error: null,
            url: 'https://flynn.boolean.careers/exercises/api/random/boolean',
        }
    },
    methods: {
        callApi(url) {
            axios
            .get(url)
            .then(response => {
                this.my_data = response.data.response;
            })
            .catch(err => {
                this.error = err.message;
            })
        }
    }
    mounted() {
        this.callApi(this.url);
})


```

```html

<div>
    <p v-if="!error">{{ my_data }}}</p>
    <p v-else>{{ error }}</p>
</div>

```