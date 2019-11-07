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
					<div class="col-md-8">
						{{dominio.nome}}
					</div>
					<div class="col-md-3 text-right">
						<span class="badge badge-info">{{(dominio.disponivel)?"Disponível":"Não Disponível"}}</span>
					</div>
					<div class="col-md-1 text-right">
						<a class="btn btn-info" v-bind:href="dominio.checkout" target="_blank">
							<span class="fa fa-shopping-cart"></span>
						</a>
					</div>
                </div>
              </li>
            </ul>

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
			},
			dominios: []
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
					this.gerarDominios();
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
			}).then(() => {
				this.itens[item.tipo].splice(this.itens[item.tipo].indexOf(item),1);
				this.gerarDominios();
			});
		},
		getItens(tipo){
			return axios({
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
		},
		gerarDominios(){
			axios({
				url: "http://localhost:4000",
				method: "post",
				data: {
					query:`
						mutation{
							dominios: gerarDominios{
								nome
								checkout
								disponivel
							}
						}
					`
				}
			}).then(response => {
				const query = response.data;
				this.dominios = query.data.dominios;
			});
		}
	},
	created(){
		this.gerarDominios();
		Promise.all([
			this.getItens("prefixo"),
			this.getItens("sufixo")	
		]).then(()=>{
			this.gerarDominios();
		});
	}
};
</script>

<style>

</style>
