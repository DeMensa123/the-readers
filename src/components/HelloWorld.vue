<template>
  <div class="container mx-auto flex flex-col items-center">
    <h1 class="text-2xl mb-4">Literatúra pre deti a mládež</h1>
    <form
      class="w-full max-w-lg text-left border-2 rounded border-gray-200 pt-2"
      @submit="checkForm"
    >
      <div class="w-full px-3 mb-6">
        <label class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2">
          Meno autora
        </label>
        <input
          class="block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
          id="author"
          type="text"
          v-model="author"
        />
      </div>
      <div class="" v-for="(book, counter) in books" v-bind:key="counter">
        <span class="" @click="deleteBook(counter)"></span>
        <div class="w-full px-3 mb-6 ">
          <label
            class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2">
            {{ counter + 1 }}. Názov knihy
          </label>
          <input
            class="block w-full bg-gray-200 text-gray-700 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
            id="title"
            type="text"
            v-model="book.title"
          />
        </div>
        <div class="w-full px-3 mb-6 ">
          <label
            class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2">
            Žáner
          </label>
          <input
            class="block w-full bg-gray-200 text-gray-700 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
            id="title"
            type="text"
            v-model="book.genre"
          />
        </div>
        <div class="w-full px-3 mb-6 flex flex-row ">
          <div class="w-1/3">
            <label
              class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2">
              Počet strán
            </label>
            <input
              class="block w-full bg-gray-200 text-gray-700 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
              id="title"
              type="text"
              v-model="book.pageCount"
            />
          </div>

          <div class="w-2/3 flex ml-8 mt-2">
            <label class="flex items-center">
              <input type="checkbox" v-model="book.damaged" />
              <span class="ml-2">Poškodená</span>
            </label>
          </div>
        </div>
      </div>
      <div class="w-full flex flex-row justify-end p-2">
        <button
          class="p-2 bg-blue-600 rounded text-white"
          type="button"
          @click="this.addBook"
        >
          Pridať knihu
        </button>
      </div>      
    </form>
    <div class="my-4">
      <button
        class="p-2 bg-red-700 rounded text-white"
        type="submit"
        @click="this.sendPostRequest"
      >
        Ulož XML
      </button>
    </div>
    <div class="my-4">
      <button
        class="p-2 bg-red-700 rounded text-white"
        type="submit"
        @click="this.readXML"
      >
        Vypis XML
      </button>
    </div>
    <div class="" v-for="(book, counter) in books" v-bind:key="counter">
      {{book.title}}
    </div>
  </div>  
</template>

<script>
export default {
  name: "Books",
  props: {},
  data: function() {
    return {
      author: '',
      books: [
        {
          title: '',
          genre: '',
          pageCount: '',
          damaged: '',
        },
      ],
    };
  },
  methods: {
    addBook() {
      this.books.push({
        title: '',
        genre: '',
        pageCount: '',
        damaged: '',
      });
    },
    readXML(){
      var xml = `<Readers>
          <author>
          <name>${this.author}</name>
          `
      
      this.books.forEach(book => {
        xml = xml.concat(
          `<book genre="${book.genre}">
              <title>${book.title}</title>
              <pageCount>${book.pageCount}</pageCount>
              <damaged>${book.damaged}</damaged>
          </book>`)
      })
     
      xml = xml.concat(`</author>
      </Readers>`)
      console.log(xml)

    },
    sendPostRequest() {
      const postUrl = "http://localhost:9000/validate";

      // var xml = `<Readers>
      //     <author>
      //     <name>${this.author}</name>
      //     <book genre="${this.genre}">
      //         <title>${this.title}</title>
      //         <pageCount>${this.pageCount}</pageCount>
      //         <damaged>${this.damaged}</damaged>
      //     </book>
      //     </author>
      // </Readers>`;

      var xml = `
          <author>
          <name>${this.author}</name>
          `

      this.books.forEach(book => {
        xml = xml.concat(
          `<book genre="${book.genre}">
              <title>${book.title}</title>
              <pageCount>${book.pageCount}</pageCount>
              <damaged>${book.damaged}</damaged>
          </book>`)
      })
      
      xml = xml.concat(`</author>`)
      console.log(xml)

      fetch(postUrl, {
        method: "POST",
        headers: {
          "Content-Type": "text/xml",
        },
        mode: "cors",
        referrerPolicy: "no-referrer",
        body: xml,
      })
        .then(function(response) {
          if (response.ok) {
            console.log(xml);
            return response.json();
          } else {
            throw new Error("Something went wrong");
          }
        })
        .then(function(data) {
          if (data) {
            console.log(data);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
