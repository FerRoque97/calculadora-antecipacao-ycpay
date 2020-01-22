<template>
  <v-container>
    <v-layout ref="form" text-center wrap>
      <v-flex v-if="$vuetify.breakpoint.xsOnly"></v-flex>

      <v-flex v-else>
        <v-img :src="require('../assets/logoyc.png')" class="my-3" contain height="200"></v-img>
      </v-flex>

      <v-flex mb-4 class="my-10">
        <h1 class="display-2 font-weight-bold mb-3">Calculadora YC Pay</h1>
      </v-flex>

      <v-container row wrap>
        <v-layout row wrap>
          <v-flex xs12 md5 pa-5 class="mx-auto">
            <v-dialog v-model="dialog" persistent max-width="380px">
              <template v-slot:activator="{ on }">
                <v-btn dark v-on="on">
                  Taxas
                  <v-icon dark right>mdi-wrench</v-icon>
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline mx-auto">Taxas</span>
                </v-card-title>
                <v-card-text>
                  <v-layout row wrap>
                    <v-flex xs12 md7 class="mx-auto">
                      <v-text-field
                        v-model="taxa.debito"
                        label="Taxa de Débito"
                        filled
                        v-mask="'#.##%'"
                      ></v-text-field>
                    </v-flex>
                    <br>
                    <v-flex xs12 md7 class="mx-auto">
                      <v-text-field
                        v-model="taxa.credito"
                        label="Taxa de Crédito Avista"
                        filled
                        v-mask="'#.##%'"
                      ></v-text-field>
                    </v-flex>
                    <br>
                    <v-flex xs12 md7 class="mx-auto">
                      <v-text-field
                        v-model="taxa.parc1"
                        label="Taxa Parcelado 2 a 6x"
                        filled
                        v-mask="'#.##%'"
                      ></v-text-field>
                    </v-flex>
                    <br>
                    <v-flex xs12 md7 class="mx-auto">
                      <v-text-field
                        v-model="taxa.parc2"
                        label="Taxa Parcelado 7 a 12x"
                        filled
                        v-mask="'#.##%'"
                      ></v-text-field>
                    </v-flex>
                    <br>
                    <v-flex xs12 md7 class="mx-auto">
                      <v-text-field
                        v-model="taxa.ant"
                        label="Taxa Antecipação"
                        filled
                        v-mask="'#.##%'"
                      ></v-text-field>
                    </v-flex>
                  </v-layout>
                </v-card-text>
                <v-card-actions class="mx-auto">
                  <div class="mx-auto">
                    <v-btn
                      class="mx-auto"
                      depressed
                      small
                      @click="dialog = false"
                      color="red"
                    >Fechar</v-btn>
                  </div>
                  <div class="mx-auto">
                    <v-btn
                      class="mx-auto"
                      depressed
                      small
                      @click="saveTaxa(), dialog = false"
                      color="primary"
                    >Salvar</v-btn>
                  </div>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-flex>

          <v-flex xs12 md8 class="mx-auto">
            <v-form>
              <v-container>
                <v-row>
                  <v-flex xs10 md8 class="mx-auto" v-if="formSe != 'Crédito'" row wrap>
                    <v-flex xs12 md5 class="mx-auto">
                      <v-text-field
                        pt-10
                        outlined
                        filled
                        v-model="valor"
                        :rules="valorRules"
                        label="Insira o Valor"
                        placeholder="Ex: 1.500,00"
                        required
                      ></v-text-field>
                    </v-flex>

                    <!-- <v-flex md1></v-flex> -->

                    <v-flex xs12 md5 class="mx-auto">
                      <v-select
                        :rules="formRules"
                        :items="form"
                        outlined
                        filled
                        v-model="formSe"
                        label="Forma de Pagamento"
                      ></v-select>
                    </v-flex>
                  </v-flex>

                  <v-flex v-else xs10 md12 row wrap class="mx-auto">
                    <v-flex xs12 md3>
                      <v-text-field
                        pt-10
                        outlined
                        filled
                        v-model="valor"
                        :rules="valorRules"
                        label="Insira o Valor"
                        placeholder="Ex: 1.500,00"
                        required
                      ></v-text-field>
                    </v-flex>

                    <v-flex md1></v-flex>

                    <v-flex xs12 md4>
                      <v-select
                        :rules="formRules"
                        :items="form"
                        filled
                        outlined
                        v-model="formSe"
                        label="Selecione a Forma de Pagamento"
                      ></v-select>
                    </v-flex>

                    <v-flex md1></v-flex>

                    <v-flex v-if="formSe == 'Crédito'" xs12 md3>
                      <v-select
                        :rules="parcelaRules"
                        :items="items"
                        v-model="qtdParc"
                        filled
                        outlined
                        label="Selecione as Parcelas"
                      ></v-select>
                    </v-flex>
                  </v-flex>
                </v-row>
              </v-container>
            </v-form>
          </v-flex>

          <v-flex xs12 md12 class="mx-auto">
            <v-btn
              :rules="parcelaRules"
              color="grey darken-4"
              class="ma-2 white--text"
              @click="accountTaxa(valor,formSe,qtdParc,taxa)"
            >
              Calcular
              <v-icon right dark>mdi-cloud-upload</v-icon>
            </v-btn>
          </v-flex>
          {{this.desserts.name}}
          <v-flex v-if="$vuetify.breakpoint.xsOnly" xs12 md12 pb-7 mt-3>
            <v-data-table
              dark
              :headers="headers"
              :items="desserts"
              hide-default-footer
              hide-default-header
              class="table table elevation-1"
            ></v-data-table>
          </v-flex>
          <v-flex v-else>
            <v-data-table
              dark
              :headers="headers"
              :items="desserts"
              hide-default-footer
              class="table table elevation-1"
            ></v-data-table>
          </v-flex>

          <v-flex>
            <v-footer dark>
              <v-col class="text-center" cols="12">
                <span>
                  <a href="https://ycpay.com.br/">YC Pay</a>© 2020. Todos os direitos reservados.
                  <br />Desenvolvido por Fernando Roque e Tullyo Cesar
                </span>
              </v-col>
            </v-footer>
          </v-flex>
        </v-layout>
      </v-container>
    </v-layout>
  </v-container>
</template>

<script>
import { mask } from "vue-the-mask";
import Swal from "sweetalert2";
export default {
  name: "HelloWorld",
  directives: {
    mask
  },
  data: () => ({
    dialog: false,
    valor: "",
    valorRules: [v => !!v || "O valor e necessário"],
    parcelaRules: [v => !!v || "Selecione o numero de Parcelas"],
    formRules: [v => !!v || "Selecione a Forma de Pagamento"],
    mask: "#,##%",
    taxa: {
      debito: null,
      credito: null,
      parc1: null,
      parc2: null,
      ant: null
    },
    taxas: [],
    formSe: "",
    qtdParc: null,
    headers: [
      {
        text: "Valor",
        align: "left",
        value: "name"
      },
      { text: "Valor Recebimento (30 Dias)", value: "vrp", align: "left" },
      {
        text: "Valor Recebimento Antecipado (1 Dia)",
        value: "vra",
        align: "left"
      },
      {
        text: "Valor de Repasse de Juros - (Recebimento Prazo)",
        value: "vrjRp",
        align: "left"
      },
      {
        text: "Valor de Repasse de Juros - (Recebimento Antecipado)",
        value: "vrjRa",
        align: "left"
      }
    ],
    valores: {
      name: "",
      vrp: "",
      vra: "",
      vrjRp: "",
      vrjRa: ""
    },
    desserts: [],
    items: [
      "A vísta",
      "2x",
      "3x",
      "4x",
      "5x",
      "6x",
      "7x",
      "8x",
      "9x",
      "10x",
      "11x",
      "12x"
    ],
    form: ["Débito", "Crédito"]
  }),
  methods: {
    Msg(tipo, titulo) {
      Swal.fire({
        icon: tipo,
        title: titulo,
        width: "300px",
        showConfirmButton: false,
        timer: 2000
      });
    },
    saveTaxa() {
      var n = this.taxas.length;
      this.taxas.splice(0, n);
      this.taxas.push(this.taxa);
      localStorage.removeItem("taxas");
      localStorage.setItem("taxas", JSON.stringify(this.taxas));
      // alert("Taxas foram salvas!");
      this.Msg("success", "Suas Taxas Foram Salvas!");
    },
    takeTaxa() {
      if (localStorage.getItem("taxas") == null) {
        
      } else {
        this.taxas = JSON.parse(localStorage.getItem("taxas"));
        // alert(JSON.stringify(this.taxas));
        this.taxa.debito = this.taxas[0].debito;
        this.taxa.credito = this.taxas[0].credito;
        this.taxa.parc1 = this.taxas[0].parc1;
        this.taxa.parc2 = this.taxas[0].parc2;
        this.taxa.ant = this.taxas[0].ant;
      }
    },
    selectTaxa(qtdParc, taxa) {
      //Seleciono a Taxa
      var parc = qtdParc;
      if (parc == "Avísta") {
        return parseFloat(this.taxa.credito);
      } else {
        parc = parseInt(parc);
        if (parc >= 2 && parc <= 6) {
          return parseFloat(this.taxa.parc1);
        } else {
          return parseFloat(this.taxa.parc2);
        }
      }
    },

    valorPrazo(valor, taxa, qtdParc) {
      //valor,taxa,qtdParc
      valor = parseFloat(valor);
      var taxaSe = this.selectTaxa(qtdParc, taxa);
      var total = ((valor * taxaSe) / 100 - valor) * -1;
      return total;
    },
    valorAnt(valor, taxa, qtdParc) {
      // calcular a Antecipação

      if (qtdParc == "Avísta") {
        var taxaAnt = parseFloat(this.taxa.ant);
        taxaAnt = taxaAnt / 100;
        var dias = 30;
        var total = this.valorPrazo(valor, taxa, qtdParc);
        var total2 = total - total * ((taxaAnt / 30) * (dias * 1 - 1));
        return total2;
      } else {
        var result = 0;
        var parc = parseInt(qtdParc);
        var taxaAnt = parseFloat(this.taxa.ant);
        taxaAnt = taxaAnt / 100;
        var dias = 30;
        var total = this.valorPrazo(valor, taxa, qtdParc);
        total = total / parc;
        for (let i = 0; i < parc; i++) {
          var val = total - total * ((taxaAnt / 30) * (dias * (i + 1) - 1));
          var result = result + val;
        }
        return result;
      }
    },
    valorRep(tip, valor, taxa, qtdParc) {
      if (tip == 1) {
        var valorR = (valor * valor) / this.valorAnt(valor, taxa, qtdParc);
        return valorR;
      } else {
        var valorR = (valor * valor) / this.valorPrazo(valor, taxa, qtdParc);
        return valorR;
      }
    },
    accountTaxa(valor, formSe, qtdParc, taxa) {
      if (valor == "" || formSe == "") {
        this.Msg("warning", "Preencha todos os campos!");
      } else if (valor != "" && formSe == "Crédito" && qtdParc == "") {
        this.Msg("warning", "Preencha todos os campos!");
      }else if(localStorage.getItem("taxas") == null){
        this.Msg("warning", "Você não possui Taxas Cadastradas!");
      } else {
        var val = parseFloat(valor);
        if (formSe == "Débito") {
          var taxa1 = parseFloat(this.taxa.debito);
          // alert("Deu certo!")
          var valorDeb = ((val * taxa1) / 100 - val) * -1;
          var d = taxa1 / 100 - 1;
          var r = (val / d) * -1;
          this.valores.name = val.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL"
          });
          this.valores.vrp = valorDeb.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL"
          });
          this.valores.vra = valorDeb.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL"
          });
          this.valores.vrjRp = r.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL"
          });
          this.valores.vrjRa = r.toLocaleString("pt-BR", {
            style: "currency",
            currency: "BRL"
          });

          var n = this.desserts.length;
          this.desserts.splice(0, n);
          this.desserts.push(this.valores);
        } else {
          if (qtdParc == "Avísta") {
            // var taxa = parseFloat(this.taxa.credito);
            // var taxaAnt = parseFloat(this.taxa.ant);
            // var valorCred = ((val * taxa) / 100 - val) * -1;
            // var valorCredAnt = ((valorCred * taxaAnt) / 100 - valorCred) * -1;

            this.valores.name = val.toLocaleString("pt-BR", {
              style: "currency",
              currency: "BRL"
            });
            this.valores.vrp = this.valorPrazo(
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", {
              style: "currency",
              currency: "BRL"
            });
            this.valores.vra = this.valorAnt(
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", {
              style: "currency",
              currency: "BRL"
            });
            this.valores.vrjRp = this.valorRep(
              2,
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", {
              style: "currency",
              currency: "BRL"
            });
            this.valores.vrjRa = this.valorRep(
              1,
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", {
              style: "currency",
              currency: "BRL"
            });

            var n = this.desserts.length;
            this.desserts.splice(0, n);
            this.desserts.push(this.valores);
          } else {
            this.valores.name = val.toLocaleString("pt-BR", {
              style: "currency",
              currency: "BRL"
            });
            this.valores.vrp = this.valorPrazo(
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", { style: "currency", currency: "BRL" });
            this.valores.vra = this.valorAnt(
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", { style: "currency", currency: "BRL" });
            this.valores.vrjRp = this.valorRep(
              2,
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", { style: "currency", currency: "BRL" });
            this.valores.vrjRa = this.valorRep(
              1,
              valor,
              taxa,
              qtdParc
            ).toLocaleString("pt-BR", { style: "currency", currency: "BRL" });

            var n = this.desserts.length;
            this.desserts.splice(0, n);
            this.desserts.push(this.valores);
          }
        }
      }
    }
  },
  created: function() {
    this.takeTaxa();
  }
};
</script>
