<template>
  <div class="container h-full mx-auto flex flex-col items-center bg-gray-100">
    <form
      class="bg-white w-full max-w-lg text-left shadow-md rounded mt-4 p-1"
      @submit="checkForm"
    >
      <h1 class="text-2xl my-4 font-semibold flex flex-row justify-center">
        Literatúra pre deti a mládež
      </h1>
      <div class="w-full px-3 mb-6">
        <label
          class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
        >
          Meno autora
        </label>
        <input
          class="block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
          id="author"
          type="text"
          v-model="author"
          required
        />
      </div>
      <div class="" v-for="(book, counter) in books" v-bind:key="counter">
        <span class="" @click="deleteBook(counter)"></span>
        <div class="w-full px-3 mb-6 ">
          <label
            class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
          >
            {{ counter + 1 }}. Názov knihy
          </label>
          <input
            class="block w-full bg-gray-200 text-gray-700 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
            id="title"
            type="text"
            v-model="book.title"
            required
          />
        </div>
        <div class="w-full px-3 mb-6 ">
          <label
            class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
          >
            Žáner
          </label>
          <input
            class="block w-full bg-gray-200 text-gray-700 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
            id="title"
            type="text"
            v-model="book.genre"
            required
          />
        </div>
        <div class="w-full px-3 mb-6 flex flex-row ">
          <div class="w-1/3">
            <label
              class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
            >
              Počet strán
            </label>
            <input
              class="block w-full bg-gray-200 text-gray-700 rounded py-3 px-4 mb-3 focus:bg-white focus:border-gray-500"
              id="title"
              type="text"
              v-model="book.pageCount"
              required
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
      <div class="w-full flex flex-row justify-between p-2">
        <button
          class="p-2 bg-blue-600 rounded text-white"
          type="button"
          @click="this.reloadPage"
        >
          Pridať nového autora
        </button>
        <button
          class="p-2 bg-blue-600 rounded text-white"
          type="button"
          @click="this.addBook"
        >
          Pridať knihu
        </button>
      </div>
    </form>
    <div class="flex flex-row w-full max-w-lg ">
      <div class="w-1/2 my-4 items-center">
        <button
          class="p-2 bg-red-700 rounded text-white transition duration-300 ease-in-out transform hover:scale-105"
          type="submit"
          @click="this.validateXML"
        >
          Validuj XML
        </button>
      </div>
      <div class="w-1/2 my-4 items-center">
        <button
          class="p-2 rounded text-white"
          :class="{
            'bg-red-300': !this.validated,
            'bg-red-700  transition duration-300 ease-in-out transform hover:scale-105': this
              .validated,
          }"
          type="submit"
          @click="this.saveXML"
          :disabled="!this.validated"
        >
          Ulož XML
        </button>
      </div>
      <div class="w-1/2 my-4">
        <button
          class="p-2 bg-red-700 rounded text-white transition duration-300 ease-in-out transform hover:scale-105"
          type="submit"
          @click="this.visualizeXML"
        >
          Vizualizuj XML
        </button>
      </div>
    </div>
    <div v-if="this.error" class="text-red-600 font-semibold m-2 text-lg">
      {{ this.error }}
    </div>
    <h3 v-if="this.saved" class="text-green-600 font-semibold m-2 text-lg">
      XML bolo uložené
    </h3>
    <div class="w-1/2 my-4">
        <button
        class="p-2 rounded text-white"
          :class="{
            'bg-green-400': !this.validated,
            'bg-green-600  transition duration-300 ease-in-out transform hover:scale-105': this
              .validated,
          }"
          type="submit"
          @click="this.signXML"
        >
          Podpísať XML
        </button>
      </div>
  </div>
</template>

<script>
export default {
  name: "Books",
  props: {},
  data: function() {
    return {
      author: "",
      books: [
        {
          title: "",
          genre: "",
          pageCount: "",
          damaged: false,
        },
      ],
      error: "",
      validated: false,
      saved: false,
    };
  },
  methods: {
    addBook() {
      this.books.push({
        title: "",
        genre: "",
        pageCount: "",
        damaged: false,
      });
    },
    reloadPage() {
      (this.author = ""),
        (this.books = [
          {
            title: "",
            genre: "",
            pageCount: "",
            damaged: false,
          },
        ]),
        window.location.reload();
    },
    createXML() {
      var xml = `<author>
          <name>${this.author}</name>
          `;

      this.books.forEach((book) => {
        xml = xml.concat(
          `<book genre="${book.genre}">
              <title>${book.title}</title>
              <pageCount>${book.pageCount}</pageCount>
              <damaged>${book.damaged}</damaged>
          </book>
          `
        );
      });

      xml = xml.concat(`</author>`);

      return xml;
    },
    validateXML() {
      const postUrl = "http://localhost:9000/validate";

      const xml = this.createXML();

      fetch(postUrl, {
        method: "POST",
        headers: {
          "Content-Type": "text/xml",
        },
        mode: "cors",
        referrerPolicy: "no-referrer",
        body: xml,
      })
        .then((response) => {
          if (response.ok) {
            console.log(xml);
            this.validated = true;
            this.error = null;
            return response.text();
          } else {
            // var error = new Error("Something went wrong");
            // this.error = res;
            console.log(xml);
            // throw new Error("Something went wrong");
            return response.text();
          }
        })
        .then((data) => {
          if (data) {
            this.error = data.split(":")[1];
            console.log(data);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    saveXML() {
      const postUrl = "http://localhost:9000/save";

      const xml = this.createXML();
      console.log(xml);
      fetch(postUrl, {
        method: "POST",
        headers: {
          "Content-Type": "text/xml",
        },
        mode: "cors",
        referrerPolicy: "no-referrer",
        body: xml,
      })
        .then((response) => {
          if (response.ok) {
            console.log(xml);
            this.saved = true;
            return response.text();
          } else {
            // var error = new Error("Something went wrong");
            // this.error = res;
            // throw new Error("Something went wrong");
            return response.text();
          }
        })
        .then((data) => {
          if (data) {
            this.error = data.split(":")[1];
            console.log(data);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    visualizeXML() {
      const postUrl = "http://localhost:9000/visualize";

      const xml = this.createXML();

      fetch(postUrl, {
        method: "POST",
        headers: {
          "Content-Type": "text/xml",
        },
        mode: "cors",
        referrerPolicy: "no-referrer",
        body: xml,
      })
        .then((response) => {
          if (response.ok) {
            return response.text();
          } else {
            return response.text();
          }
        })
        .then((data) => {
          if (data) {
            this.error = data.split(":")[1];
            console.log(data);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    signXML() {
      const postUrl = "http://localhost:9000/sign";

      const xml = this.createXML();

      fetch(postUrl, {
        method: "POST",
        headers: {
          "Content-Type": "text/xml",
        },
        mode: "cors",
        referrerPolicy: "no-referrer",
        body: xml,
      })
        .then((response) => {
          if (response.ok) {
            return response.text();
          } else {
            return response.text();
          }
        })
        .then((data) => {
          if (data) {
            this.error = data.split(":")[1];
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
