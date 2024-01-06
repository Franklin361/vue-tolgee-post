# Building a multi-language app with Vue JS. 🌐

Today I will present you an easy way to create applications that support multiple languages, using Vue JS.

## Technologies to be used.

- Vue **3.3.11**
- @tolgee/vue **5.19.0**
- Vite JS **5.0.8**
- TypeScript **5.2.2**
- Tailwind CSS **3.4.0**

## Creating the project

To create the project with Vue, we will use **vite js**.
We will name the project: **`multi-lang-app`** (optional, you can name it whatever you like).

```bash
npm create vite@latest
```

After we set the name, we select the Vue option and then the TypeScript option (although it is not necessary for this occasion).
We create the project with Vite JS and select React with TypeScript.  
Then we run the following command to navigate to the directory just created.

```bash
cd multi-lang-app
```

Then we install the dependencies.

```bash
npm install
```

Then we open the project in a code editor (in my case VS code).

```bash
code .
```

### Installing Tailwind

We just follow the steps indicated in the [Vite documentation](https://tailwindcss.com/docs/guides/vite#vue)
First we install the dependencies.

```bash
npm install -D tailwindcss postcss autoprefixer
```

Then run this command to create the tailwind configuration files.

```bash
npx tailwindcss init -p
```

Then in our `tailwind.config.js` file we add the following settings

```tsx
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

And in our `style.css` file we add:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

And that's enough.

## First steps.

First we are going to create a simple TO-DO list.

We create a new file `src/components/MainContent.vue`.
In which we will define two new states:

- **task**: This is the description of the task.
- **collectionTasks**: an array of tasks (and a task is composed of object that contains the description and if it is completed).

```vue
<script setup lang="ts">
import { ref } from 'vue'

interface Task {
  task: string
  isCompleted: boolean
}

const task = ref('')
const collectionTasks = ref<Task[]>([])
</script>

<template>
</template>
```

After that, we create two simple functions.

- **createTask**: adds a new task to the list and resets the input where you enter the task.
- **handleToggleComplete**: marks the task as completed.

```vue
<script setup lang="ts">

// ...

const createTask = () => {

  if(task.value.trim().length === 0) return

  collectionTasks.value.push({
    task: task.value,
    isCompleted: false
  })

  task.value = ''
}

const handleToggleComplete = (index:number) => {
  const taskSelected = collectionTasks.value.find( (_,i) => i === index )
  if(taskSelected) {
    taskSelected.isCompleted = !taskSelected?.isCompleted
  }
}
</script>

<template>
</template>
```

Now we continue with HTML.

> Note: At this point you are supposed to have a good understanding of how to use Vue, so you should not be surprised by such characteristics that Vue uses, like the @submit.prevent, @click, v-model, v-show, v-for, v-bind (using its short form ":") or the "Mustache" syntax for text interpolation.

```vue
<script setup lang="ts">
// ...
</script>

<template>
  <div class="flex items-center flex-col gap-2 p-5">

    <h1 class="text-6xl font-black mt-28 mb-10 text-wrap">
      THIS IS THE MAIN TITLE
    </h1>

    <main class="max-w-2xl ">
      <form
        @submit.prevent="createTask"
        class="flex items-center gap-5"
      >
        <input
          type="text"
          placeholder="TASK PLACEHOLDER"
          autofocus
          v-model="task"
          class="p-3 px-4 text-xl rounded-lg flex-1 min-w-[420px]"
        />
        <button class="bg-blue-500 p-3 px-4 rounded-md hover:bg-blue-600">
          ADD NEW TASK
        </button>
      </form>

      <section
        class="bg-[#272932] p-5 rounded-md my-10"
        v-show="collectionTasks.length !== 0"
      >
        <h2
          class="mt-2 mb-5 text-3xl font-bold text-white/50 border-b pb-3 border-white/50"
        >
          SECTION LIST TASK TITLE
        </h2>

        <div
          v-for="(item, index) in collectionTasks"
          :key="index"
          class="flex items-center gap-5"
        >
          <span
            :class="['p-2 text-xl cursor-pointer', item.isCompleted && 'line-through']"
            @click="handleToggleComplete(index)"
          >
            👉 {{ item.task }}</span
          >

          <span
            v-show="item.isCompleted"
            class="p-2 rounded-lg bg-green-500/20 text-green-300 border-green-500 border text-xs"
          >
            COMPLETED
          </span>
        </div>
      </section>
    </main>
  </div>
</template>
```

Note that in the code above I put certain words in capital letters, this is because it is there where we want to apply the language change.
And with this we finish our mini application, we only need to show it in `App.vue`.

Inside the `src/App.vue` file, we delete everything and we place the following:

```vue
<script setup lang="ts">
import MainContent from './components/MainContent.vue';
</script>

<template>
  <MainContent />
</template>
```

## Configuring the translations for the app.

Before we continue with the code, let's configure the translations, for this, we will use Tolgee.

> Tolgee is a localization platform that allows you to translate your application into any language without modifying your code. It is designed for use with web applications, but can also be used with mobile apps and desktop applications."

You can learn more do about this platform on their website [Tolgee](https://tolgee.io/).

1. First create an account at [https://app.tolgee.io/sign_up](https://app.tolgee.io/sign_up).

Once the account is created it will take you to your projects, which will not have any created yet.

2. Add a new project.

- Add a name to the project (example: demo-vue).
- Add the languages you want to configure.
- Select a base language.
- Finally click on the "Save" button

Once the project is created, you will be taken to the project dashboard.
Now go to the "Translations" part which should be on the left side menu.

3. Add translations.

Once in the "Translations" section, press the "+" button to add a new translation. You will be asked to add the following:

- **key**: this is the keyword that identifies the translation and **must be unique**. Normally they follow a convection that uses "about_page_title", see more about it in the [documentation](https://tolgee.io/platform/translation_keys/keys#naming-conventions)
- **namespaces (optional)**: is a key that helps to separate translations in multiple files, it is useful when you have a large app that contains many translations (more than 1000 keys)
- **tags**: that can be used to group keys.
- **languages text(s)**: the text that should be displayed in each language.
  - You can select the languages you want to display here by selecting them from the drop-down menu at the top. If you do not specify the text for a language, the key will be marked as "Untranslated" for that language.

Note that when you add the text in the field of the language you selected as the "base language" and then move to another field to add another language, a panel with two sections will be displayed

- **TRANSLATION MEMORY**: looks for similar texts in your project and suggests them to you. This helps you to reuse translations and save time. see more in the section [translation memory](https://tolgee.io/platform/translation_process/translation_memory)
- **MACHINE TRANSLATION**: is a way to translate content using external services and get a rough translation of the content.

Only select one option, if you do not know the exact translation.

Finally press "Save" to add the translation.

For this project I will add 3 languages "Mexican Spanish", "English" and "French".

## Adding Tolgee to our project.

To add Tolgee to our project is very simple, we execute:

```bash
npm install @tolgee/vue
```

Now let's create a new `src/lib/tolgee.ts` file and add the following configuration.

First we are going to call the Tolgee function.

```ts
import { Tolgee } from '@tolgee/vue';

export const tolgee = Tolgee()
```

Next we are going to configure some plugins for Tolgee.

- [DevTools](https://tolgee.io/js-sdk/api/web_package/plugins#devtools): combination of several plugins that will help us in development mode, such as communicating our app with the Tolgee platform. This plugin is omitted in production by default.
- [FormatSimple](https://tolgee.io/js-sdk/api/core_package/format-simple): This is a formatter that enables us to pass variables into translations.

To define a plugin we use the **use()** method.

```ts
import { Tolgee, DevTools, FormatSimple, BackendFetch } from '@tolgee/vue';

export const tolgee = Tolgee()
  .use(DevTools())
  .use(FormatSimple())
```

Then we call the init method, passing it an object with the following properties.

- **language**: the initial language of your app.
- **apiUrl**: a URL provided by tolgee, to connect to the platform.
- **appKey**: a KEY provided by tolgee, to connect to the platform.

This init() function has more [options](https://tolgee.io/js-sdk/api/core_package/options), but those mentioned are sufficient for the moment.

```ts
import { Tolgee, DevTools, FormatSimple, BackendFetch } from '@tolgee/vue';

export const tolgee = Tolgee()
  .use(DevTools())
  .use(FormatSimple())
  .init({
    language: 'es-MX',
    apiUrl: '',
    apiKey: ''
  });
```

### Getting the API KEY.

Back to the Tolgee platform, look for the **integrate** section in the side menu.

A series of steps will appear:

1. Choose the technology you are going to use for your project.
2. Then you will be asked to select an **API KEY**. If you already have one created, if not, just select the "**Create new +**" option. There you can configure the name, expiration and scope.
3. It will show you a brief Quick start. In that part it will show you both the **API_URL** and **API_KEY** variables.

Just copy the variables into a **.env** file inside your project. And finally we make reference to those environment variables.

```ts
import { Tolgee, DevTools, FormatSimple, BackendFetch } from '@tolgee/vue';

export const tolgee = Tolgee()
  .use(DevTools())
  .use(FormatSimple())
  .init({
    language: 'es-MX',
    apiUrl: import.meta.env.VITE_APP_TOLGEE_API_URL,
    apiKey: import.meta.env.VITE_APP_TOLGEE_API_KEY
  });
```

### Finishing the Tolgee configuration

Now in our `src/App.vue` file we use the tolgee plugin we created in the Vue app as follows:

```tsx
import { createApp } from 'vue';
import './style.css';
import App from './App.vue';

import { VueTolgee } from '@tolgee/vue';
import { tolgee } from './lib/tolgee';

const app = createApp(App);

app.use(VueTolgee, { tolgee });

app.mount('#app');
```

### Adding the provider

Now we need to add a provider in `src/App.vue` and wrap our **<MainContent/>** component.

```vue
<script setup lang="ts">
import { TolgeeProvider } from '@tolgee/vue';
import MainContent from './components/MainContent.vue';
</script>

<template>
  <TolgeeProvider>
    <MainContent />
  </TolgeeProvider>
</template>
```

Now inside the provider we need to specify a fallback component that will be displayed while loading the default language translations.

> Note that I created a separate component called Loading in `src/components/Loading.vue`

```vue
<script setup lang="ts">
import { TolgeeProvider } from '@tolgee/vue';
import MainContent from './components/MainContent.vue';
import Loading from './components/Loading.vue';
</script>

<template>
  <TolgeeProvider>

    <template v-slot:fallback>
      <Loading />
    </template>

    <MainContent />
  </TolgeeProvider>
</template>
```

And now we are ready to translate.

## Translating the texts

Once the configuration is finished, we have access to several ways to translate the texts, in this case I will use the global function [**$t**](https://tolgee.io/js-sdk/integrations/vue/translating#global-t-function), because I don't need to import anything thanks to the configuration we did.
There are also other ways, like using a [component T ](https://tolgee.io/js-sdk/integrations/react/api#t-component)or a [composable useTranslate](https://tolgee.io/js-sdk/integrations/react/api#hook-usetranslate)

Returned in the `src/components/MainContent.vue` file, we use the global function **$t** in this way:

- Inside the function, we send it the key we defined in the Tolgee platform.

```vue
<h1 class="text-6xl font-black mt-28 mb-10 text-wrap">
  {{ $t('title_page') }}
</h1>
```

And this way we can see how the title is correctly displayed in the default language we configured.

Now let's replace the other texts of the elements:

- the **placeholder** of the input

```vue
<template>
<!-- all the rest ... -->
  <input
    type="text"
    :placeholder="$t('placeholder_task_input')"
    autofocus
    v-model="task"
    class="p-3 px-4 text-xl rounded-lg flex-1 min-w-[420px]"
  />
  <!-- all the rest ... -->
</template>
```

- the button label

```vue
<button class="bg-blue-500 p-3 px-4 rounded-md hover:bg-blue-600">
  {{ $t('button_add_task') }}
</button>
```

- the title of the listing section

```vue
<h2
  class="mt-2 mb-5 text-3xl font-bold text-white/50 border-b pb-3 border-white/50"
>
  {{ $t('title_section_list_tasks') }}
</h2>
```

And so on and so forth what you would like to translate.

## Changing the language.

We can already show the texts, now we need to select another language to show the other translations.
For it we will create a component `src/components/SelectLang.vue`.

First we will create the functionality.
We will be using a tolgee composable, [**useTolgee**](https://tolgee.io/js-sdk/integrations/react/api#hook-usetolgee).
This composable returns an instance of Tolgee allowing us to subscribe to different [events](https://tolgee.io/js-sdk/api/core_package/events)

> Note how we specify "language", which means we only want to listen for the event when a language changes.
> Also note that we are passing an array to useTolgee, because we can listen for multiple events.

Next we create a function that will receive an event () and we will execute the **changeLanguage** function of tolgee, sending the value of the input.
Basically we will use a **select** element and every time a change is made the **changeLanguage** will be executed, passing the value we have selected.

```vue
<script setup lang="ts">
import { useTolgee } from '@tolgee/vue';

const tolgee = useTolgee(['language']);

const changeLanguage = (e: Event) => {
  tolgee.value.changeLanguage((e.target as HTMLSelectElement).value);
};
</script>
```

Finally we just implement the template.

- Note that in the value attribute of the select, we are calling the getLanguage() function of tolgee, which provides us with the current language we have configured (**NOT** the language of your browser), which is 'en-MX'.

- Note also that in the @change event of the select we are calling the function we created **"changeLanguage "**.
- Finally note that in each option element must carry a **value** attribute that refers to the language, just as it is in the Tolgee platform.

```vue
<script setup lang="ts">
// ---
</script>

<template>
  <select
    :value="tolgee.getLanguage()"
    @change="changeLanguage"
    class="p-1 rounded-md text-lg font-semibold"
  >
    <option value="es-MX">🇲🇽 Spanish</option>
    <option value="en">🇺🇲 English</option>
    <option value="fr">🇫🇷 French</option>
  </select>
</template>
```

Ready, we have our component to change the language of our app.

Now we are going to create a small NavBar to place it.

Inside `src/components/NavBar.vue` and place the following.

```vue
<script setup lang="ts">
import SelectLang from './SelectLang.vue';
</script>

<template>
  <nav
    class="flex justify-between items-center gap-5 bg-black/50 backdrop-blur-md fixed top-0 left-0 w-full p-7 px-10"
  >
    <div class="flex gap-5 items-center">
      <span>{{ $t('label_select_idiom') }}</span>

      <SelectLang />

    </div>
  </nav>
</template>
```

> Don't forget to place the _Navbar.vue_ component in `src/components/MainContent.vue`.

## Using variables in translations

To use variables you have to make settings in the Tolgee platform.

> Tolgee follow the message format [ICU](https://tolgee.io/platform/translation_process/icu_message_format) learn more about this format to learn more rules, as here I will only show you the most basic one.

Suppose you want to create something like this:

"Hello, 'VAR_NAME' is my name"

VAR_NAME being the variable you want to display and it can be any name.

Well when you are creating the translations in Tolgee, and you want to add a variable, you would do it this way in each translation:

- You enclose the variable between these '{}' symbols.
- Note that inside the square brackets is the name of the variable "name". The name that you put it must be the same in each translation and the same one that we will use in our app to make reference to that variable.

```txt
Hello, {name} is my name.
```

Now let's go to `src/components/NavBar.vue`.
Let's create a simple variable.

```vue
<script setup lang="ts">
// ....
const userName = 'Barry Allen'
</script>

<template>
  <!-- .... -->
</template>
```

And now to place a variable we will do it this way:

```vue
<span>{{ $t('title_navbar',{ name: userName })  }}</span>
```

We continue sending the key as the first parameter, then an object with the name of the property exactly as you defined it in the Tolgee platform, which in my case I put '{name}'.

And so we can use variables in our translations.

```vue
<script setup lang="ts">
import SelectLang from './SelectLang.vue';

const name = 'Barry Allen'
</script>

<template>
  <nav
    class="flex justify-between items-center gap-5 bg-black/50 backdrop-blur-md fixed top-0 left-0 w-full p-7 px-10"
  >
    <span>{{ $t('title_navbar',{ name })  }}</span>

    <div class="flex gap-5 items-center">
      <span>{{ $t('label_select_idiom') }}</span
      ><SelectLang />
    </div>
  </nav>
</template>
```

## Preparing for production

When you are going to deploy your app to production, you will **NOT** include the environment variables. This is because the SDK and environment variables should only be used in development and not in production.

For production you should use the [data exported from the Tolgee platform](https://tolgee.io/platform/projects_and_organizations/export).

You have these options:

1. **Export the data manually from the Tolgee platform.**

In the Tolgee platform, in the side menu there is an "Export" option, which takes you to configure which languages you want to export and other settings.
Once you configure and export the translations in files such as JSON format files, you place them in the `public/i18n` folder.

And you make some settings in the `src/lib/tolgee.ts` file something like this.

```ts
export const tolgee = Tolgee()
  // ...
  .init({
    // ...
    staticData: {
      'es-MX': () => import('./i18n/es-MX.json'),
      fr: () => import('./i18n/fr.json'),
      en: () => import('./i18n/en.json'),
    },
  });
```

2. **Use "BackendFetch" plugin**

There is another easier way if you don't want to download those files. And that is by using the **BackendFetch**z plugin.

In the Tolgee platform, go to the side menu and look for the "Developer settings" section.
Once there, in the ["CONTENT DELIVERY"](https://tolgee.io/platform/projects_and_organizations/content_delivery) part you will hit the "+ CONTENT DELIVERY" button. Basically it is to deploy your translation files to a cloud storage so you can use them in your app.

When you press the button it will ask you for a couple of configurations such as the name, what languages you are going to display, in what format, among others.
Once you finish configuring that, and press "SAVE", it will create a CDN which you will use in your app.

Using the BackendFetch plugin, we pass an object with the prefix property and place the CDN we created.

```ts
export const tolgee = Tolgee()
// ...
.use(
    BackendFetch({
      prefix: 'https://cdn.tolg.ee/19c2f74a3835f11cab85f',
    })
)
.init({
  // ....
});
```

The best thing is that, with BackendFetch and Content delivery, when you update your translations in Tolgee, they will be reflected in your app.

Now you can deploy your app.

## Simple demo.

[https://vue-tolgee-multi-lang.netlify.app/](https://vue-tolgee-multi-lang.netlify.app/)

## Source code.

[https://github.com/Franklin361/vue-tolgee](https://github.com/Franklin361/vue-tolgee)
