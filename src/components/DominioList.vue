<template>
  <div>

    <div id="main">
      <div class="container">

        <div class="row">
          
          <div class="col-md">
            <AppItemList title="Prefixo" v-bind:items="prefixos" v-on:addItem="addPrefixo" v-on:deleteItem="deletePrefixo"></AppItemList>
          </div>
          
          <div class="col-md">
            <AppItemList title="Sufixo" v-bind:items="sufixos" v-on:addItem="addSufixo" v-on:deleteItem="deleteSufixo"></AppItemList>
          </div>

        </div>
        
        <br>

        <h5>Domínios <span class="badge badge-info">{{dominios.length}}</span> </h5>
        <div class="card">
          <div class="card-body">
            
            <ul class="list-group">
              <li class="list-group-item" v-for="dominio in dominios" v-bind:key="dominio.nome">
                <div class="row">
                  <div class="col-md">
                    {{dominio.nome}}
                  </div>
                  <div class="col-md text-right">
                    <a class="btn btn-info" v-bind:href="dominio.chekout" target="_blank">
                      <span class="fa fa-shopping-cart"></span>
                    </a>
                  </div>
                </div>
              </li>
            </ul>
            <br>

          </div>
        </div>
        
      </div>
    </div>
  </div>
</template>

<script>
import AppItemList from "./AppItemList";
import axios from "axios/dist/axios";
export default {
	name: "app",
	components:{
		AppItemList
	},
	data(){
		return{
			prefixos: [],
			sufixos:[]
		};  
	},
	methods:{
		addSufixo(sufixo){
			if(sufixo != ""){
				this.sufixos.push(sufixo);
			}
		},
		addPrefixo(prefixo){
			if(prefixo != ""){
				this.prefixos.push(prefixo);
			}
		},
		deletePrefixo(prefixo){
			this.prefixos.splice(this.prefixos.indexOf(prefixo),1);
		},
		deleteSufixo(sufixo){
			this.sufixos.splice(this.sufixos.indexOf(sufixo),1);
		}
	},
	computed:{
		dominios(){
			const dominios = [];
			for(const prefixo of this.prefixos){
				for(const sufixo of this.sufixos){
					const nome = prefixo+sufixo;
					const link = nome.toLowerCase();
					const chekout = `https://checkout.hostgator.com.br/?a=add&sld=${link}&tld=.com.br`;
					dominios.push({nome, chekout});
				}
			}
			return dominios;
		}
	},
	created(){
		//console.log(axios);
		axios({
			url: "http://localhost:4000",
			method: "post",
			data: {
				query:`
				{
					prefixos : itens(tipo:"prefixo"){
						id
						tipo
						descricao
					}
					sufixos : itens(tipo:"sufixo"){
						id
						tipo
						descricao
					}
				}

				`
			}
		}).then(response => {
			const query = response.data;
			console.log(query);
			this.prefixos = query.data.prefixos.map(item => item.descricao);
			this.sufixos = query.data.sufixos.map(item => item.descricao);
		});
	}
};
</script>

<style>

</style>
