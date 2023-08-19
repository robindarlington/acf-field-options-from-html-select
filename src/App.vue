<template>
  <header>
    <h1>Convert An HTML Select element to ACF Select Field Options</h1>
  </header>

  <main>
    <div class="row">
      <div class="col">
        <p>Paste HTML code <b>containing a single select element</b> here:</p>
        <textarea
          id="in_code"
          @focus="$event.target.select()"
          @mouseup="$event.preventDefault()"
          placeholder='<select class="select2 form-control" name="raison_du_contact" id="raison_du_contact">
                                    <option></option>
                                    <option>Projet PME</option>
                                    <option>Projet conversion</option>
                                    <option>Projet installation</option>
                                    <option>Projet diversification</option>
                                    <option>Info techniques prod</option>
                                    <option>Réglementation</option>
                                    <option>Aides SAB</option>
                                    <option>Aides JA</option>
                                    <option>Aides investissement</option>
                                    <option>Aides autres</option>
                                    <option>Info filières</option>
                                    <option>Info marchés</option>
                                    <option>Autre : à précise</option>
                                </select>'
          v-model="inputCode"
          @change="updateOut()"
        ></textarea>
      </div>
      <div class="col">
        <p>Copy ACF options from here:</p>
        <textarea
          id="out_options"
          readonly
          @mouseup="$event.preventDefault()"
          @focus="copy($event)"
          >{{ outputCode }}</textarea
        >
      </div>
    </div>
  </main>
</template>
<script setup>
import { parse } from 'node-html-parser'
import { onMounted, ref } from 'vue'

console.log(`Hello fellow human!\n`, `You can reach me at mail@robindarlington.com `)

let inputCode = ``
let outputCode = ref('')
console.log(inputCode)

const updateOut = () => {
  if (!inputCode) {
    outputCode.value = ``
    return
  }
  let root = parse(inputCode)
  let select = root.querySelector('select')
  if (!select) {
    console.log('No select element found.')
    return
  }
  let options = select.childNodes.filter((item) => item.rawTagName === 'option')
  if (!options) {
    console.log('No options found in select element.')
    return
  }
  let outArr = []

  options.forEach((opt) => {
    if (opt.attributes.value) {
      outArr.push({
        val: opt.text,
        key: opt.attributes.value
      })
    } else {
      outArr.push({
        val: opt.text,
        key: false
      })
    }
  })
  // outputCode.value = outArr.join('\n');
  let flatArr = outArr.map((el) => {
    let str = el.val
    if (el.key) {
      str = el.key + ':' + str
    }
    return str
  })
  outputCode.value = flatArr.join('\n')
  console.log(outputCode.value)
}

function copy(event) {
  event.target.select()
  document.execCommand('copy')
}

// onMounted(() => {
//   updateOut(inputCode);
// });
</script>
