<template>
  <div>

    <div id="main">
      <div class="container">

        <div class="row">
          
          <div class="col-md">
            <AppItemList title="Prefixo" v-bind:items="itens.prefixo" tipo="prefixo" v-on:addItem="addItem" v-on:deleteItem="deleteItem"></AppItemList>
          </div>
          
          <div class="col-md">
            <AppItemList title="Sufixo" v-bind:items="itens.sufixo" tipo="sufixo" v-on:addItem="addItem" v-on:deleteItem="deleteItem"></AppItemList>
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
			itens:{
				prefixo: [],
				sufixo:[]
			}
		};  
	},
	methods:{
		addItem(item){
			if(item.descricao != ""){
				axios({
					url: "http://localhost:4000",
					method: "post",
					data: {
						query:`
							mutation ($item: ItemInput) {
								newItem: saveItem(item: $item){
									id
									tipo
									descricao
								}
							}
						`,
						variables:{ item }
					}
				}).then(response => {
					const query = response.data;
					const newItem = query.data.newItem;
					this.itens[item.tipo].push(newItem);
				});
			}
		},
		deleteItem(item){
			axios({
					url: "http://localhost:4000",
					method: "post",
					data: {
						query:`
							mutation ($id: Int){
								deleted: deleteItem(id: $id)
							}
						`,
						variables:{ id: item.id }
					}
				}).then(response => {
					this.getItens(item.tipo);
				});
		},
		getItens(tipo){
			axios({
				url: "http://localhost:4000",
				method: "post",
				data: {
					query:`
						query ($tipo: String){	
							itens(tipo:$tipo){
								id
								tipo
								descricao
							}
						}
					` ,
					variables:{ tipo }
				}
			}).then(response => {
				const query = response.data;
				this.itens[tipo] = query.data.itens;
			});
		}
	},
	computed:{
		dominios(){
			const dominios = [];
			for(const prefixo of this.itens.prefixo){
				for(const sufixo of this.itens.sufixo){
					const nome = prefixo.descricao+sufixo.descricao;
					const link = nome.toLowerCase();
					const chekout = `https://checkout.hostgator.com.br/?a=add&sld=${link}&tld=.com.br`;
					dominios.push({nome, chekout});
				}
			}
			return dominios;
		}
	},
	created(){
		this.getItens("prefixo");
		this.getItens("sufixo");
	}
};
</script>

<style>

</style>
