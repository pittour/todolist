<template>
  <div>
    <div class="enter">
      <input
        type="text"
        name="todo"
        id="todo"
        maxlength="100"
        v-model="lol"
        v-on:keydown.enter="add"
        placeholder="Entrez votre texte ici..."
      />
      <i type="submit" class="fas fa-plus-circle" v-on:click="add"></i>
    </div>
    <div v-show="titre" class="title">
    <h1 id="title">Ma liste</h1><i class="fas fa-pencil-alt editTitle" v-on:click="editTitle"></i>
    </div>
    <table>
      <tr v-for="element in list" :key="element">
        <td>
          <span v-show="ranking"> {{ element.id }}.</span>
          <input
            type="checkbox"
            v-bind:id="element.id"
            name=""
            v-model="element.check"
          />
        </td>

        <td>
          <label v-bind:for="element.id" v-bind:id="element.id + 'label'">
            <span v-bind:id="element.id + 'span'"> {{ element.task }}</span>
          </label>
        </td>

        <td>
          <i class="fas fa-pencil-alt" v-on:click="edit(element)"></i>

          <i class="fas fa-strikethrough" v-on:click="cross(element)"></i>
          <i class="fas fa-minus-circle" v-on:click="del(element)"></i>
          <i
            class="fas fa-chevron-circle-up"
            v-on:click="move(element.id - 1, element.id - 2)"
          ></i>
          <i
            class="fas fa-chevron-circle-down"
            v-on:click="move(element.id - 1, element.id)"
          ></i>
        </td>
      </tr>
    </table>
    <div class="controls">
      <a href="" v-on:click="enableRanking"
        ><i class="fas fa-medal"></i>Activer/Désactiver le ranking</a
      >
      <a href="" v-on:click="crossAll"
        ><i class="fas fa-strikethrough"></i>Barrer/Débarrer la séléction</a
      >
      <a href="" v-on:click="delSel"
        ><i class="fas fa-trash-alt"></i>Enlever la sélection</a
      >
      <a href="" v-on:click="delAll"
        ><i class="fas fa-trash-alt"></i>Enlever tout</a
      >
    </div>
    <HelloWorld />
  </div>
</template>


<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },

  data() {
    return {
      lol: "",
      check: null,
      list: [],
      taskchanged: "",
      ranking: false,
      titre: false,
    };
  },

  methods: {
    // on attribue un id unique à chaque element de la liste
    setID() {
      this.list.forEach((item) => (item.id = this.list.indexOf(item) + 1));
    },

    // si l'utilisateur entre du texte, on le pousse dans le tableau avec un ID et un booléen qui servira à voir si la case associée est cochée ou non (si la case est cochée v-model renvoie, check = true sinon false.)
    add() {
      if (this.lol != "") {
        this.list.push({
          task: this.lol,
          id: null,
          check: this.check,
        });
      }
      this.lol = "";
      this.setID();
      this.titre = true;
      document.getElementById("todo").focus();
    },

    // Bien sur quand on delete un item, on oublie de metre à jour l'ID de tous les items restants
    del(a) {
      this.list = this.list.filter((e) => e != a);
      this.setID();
    },

    cross(d) {
      document.getElementById(d.id + "span").classList.toggle("crossed");
    },

    crossAll() {
      event.preventDefault();
      // let allSpan = document.querySelectorAll("'#'+this.list.id+'span'")
      // console.log(allSpan)
      this.list.forEach((item) => {
        if (item.check == true) {
          document.getElementById(item.id + "span").classList.toggle("crossed");
          item.check = false;
        }
      });
    },

    delAll() {
      event.preventDefault();
      this.list = [];
    },

    // on parcourt notre tableau, si la case d'un itrem renvoie true (est cochée) on met à jour notre liste. Puis on met à jour l'id des items qui restent
    delSel() {
      event.preventDefault();
      this.list.forEach((item) => {
        if (item.check == true) {
          this.list = this.list.filter((e) => e.check != true);
          this.setID();
        }
      });
    },

    // On enregistre dans une variable ce que l'on va déplacer. Puis on le supprime du tableau. Enfin, on le place à la position souhaitée grace à notre variable. On met à jour les id.
    move(fromIndex, toIndex) {
      if (fromIndex > -1 && fromIndex < this.list.length && toIndex > -1) {
        let up = this.list[fromIndex];
        this.list.splice(fromIndex, 1);
        this.list.splice(toIndex, 0, up);
        this.setID();
      }
    },

    enableRanking() {
      event.preventDefault();
      this.ranking = !this.ranking;
      let medal = document.querySelectorAll(".fa-medal");
      medal.forEach((e) => e.classList.toggle("ranking"));
    },

    editTitle() {
      document.getElementById("title").toggleAttribute("contenteditable")
      document.getElementById("title").focus()
      document
        .getElementById("title")
        .addEventListener("keydown", function (e) {
          if (e.code == "Enter") {
            e.preventDefault();
            document.getElementById("title").blur();
          } 
          else if (
            document.getElementById("title").innerText.trim().length >
              35 &&
            e.code != "Backspace"
          ) {
            e.preventDefault();
          }
        })
        document
        .getElementById("title")
        .addEventListener("blur", function () {
          document
            .getElementById("title")
            .removeAttribute("contenteditable");
        })
    },

    edit(c) {
      // quand on clique sur modifier, on autorise la span à etre éditée et on se place dessus
      document.getElementById(c.id + "span").toggleAttribute("contenteditable");
      document.getElementById(c.id + "span").focus();
      // on empeche que le click sur le label coche la case si on es en train de le modifier
      document.getElementById(c.id + "label").addEventListener("click", label);
      function label(event) {
        event.preventDefault();
      }

      // Si on appuie sur entrée pdt qu'on modifie, on sort de la span
      document
        .getElementById(c.id + "span")
        .addEventListener("keydown", function (e) {
          if (e.code == "Enter") {
            e.preventDefault();
            document.getElementById(c.id + "span").blur();
          } else if (
            document.getElementById(c.id + "span").innerText.trim().length >
              100 &&
            e.code != "Backspace"
          ) {
            e.preventDefault();
          }
        });

      // lorsqu'on sort de la span, on ne permet plus sa modification et on remet le click sur le label à son état d'origine
      document
        .getElementById(c.id + "span")
        .addEventListener("blur", function () {
          document
            .getElementById(c.id + "span")
            .removeAttribute("contenteditable");
          document
            .getElementById(c.id + "label")
            .removeEventListener("click", label);
        });
    },
  },
};
</script>

<style>
</style>
