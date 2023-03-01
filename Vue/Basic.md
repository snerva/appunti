```js
npm create vite@latest my-vue-app -- --template vue
cd my-vue-app
npm install --save-dev vue-3-options-preset
npx preset-options-api -b   <-(to add bootstrap)
npm i
npm add -D sass <-(to add sass)
(to add fontawesome):
npm i --save @fortawesome/fontawesome-svg-core
npm i --save @fortawesome/free-solid-svg-icons
npm i --save @fortawesome/free-regular-svg-icons
npm i --save @fortawesome/free-brands-svg-icons
npm i --save @fortawesome/vue-fontawesome@latest-3
npm run dev
```
# Set up fontawesome library on vue 3.x
```js
* Set up using Vue 3 */
import { createApp } from 'vue'
import App from './App.vue'

/* import the fontawesome core */
import { library } from '@fortawesome/fontawesome-svg-core'

/* import font awesome icon component */
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

/* import specific icons */
import { faUserSecret } from '@fortawesome/free-solid-svg-icons'
import { faTwitter } from '@fortawesome/free-brands-svg-icons'

/* add icons to the library */
library.add(faUserSecret, faTwitter)

createApp(App)
.component('font-awesome-icon', FontAwesomeIcon)
.mount('#app') 
```
in html

<!-- Add Icons using String format -->
<font-awesome-icon icon="fa-brands fa-twitter" />
<font-awesome-icon icon="fa-solid fa-user-secret" />