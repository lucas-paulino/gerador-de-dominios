<template>
  <div>
    <div id="slogan" class="text-center">
      <h1>Gerador de nomes</h1>
      <h6>Gerador de nomes utilizando Vue, Node e GraphQL</h6>
    </div>
    <div id="main">
      <div class="container">
        <div class="row">
          
          <div class="col-md">
            <h5> Prefixo <span class="badge badge-info">{{prefixos.length}}</span> </h5>
            <div class="card">
              <div class="card-body">

                <ul class="list-group">
                  <li class="list-group-item" v-for="prefixo in prefixos" v-bind:key="prefixo">
                    <div class="row">
                      <div class="col-md">
                        {{prefixo}}
                      </div>
                      <div class="col-md text-right">
                        <button class="btn btn-info" v-on:click="deletePrefixo(prefixo)"><span class="fa fa-trash"></span></button>
                      </div>
                    </div>
                  </li>
                </ul>
                
                <br>
                
                <div class="input-group">
                  <input class="form-control" v-model="prefixo" v-on:keyup.enter="addPrefixo(prefixo)" type="text" placeholder="Digite o Prefixo...">
                  <div class="input-group-append">
                    <button class="btn btn-info" v-on:click="addPrefixo(prefixo)"><span class="fa fa-plus"></span></button>
                  </div>
                </div>

              </div>
            </div>
          </div>
          
          <div class="col-md">
            <h5>Sufixo <span class="badge badge-info">{{sufixos.length}}</span> </h5>
            <div class="card">
              <div class="card-body">
                
                <ul class="list-group">
                  <li class="list-group-item" v-for="sufixo in sufixos" v-bind:key="sufixo">
                    <div class="row">
                      <div class="col-md">
                        {{sufixo}}
                      </div>
                      <div class="col-md text-right">
                        <button class="btn btn-info" v-on:click="deleteSufixo(sufixo)"><span class="fa fa-trash"></span></button>
                      </div>
                    </div>
                  </li>
                </ul>
                
                <br>

                <div class="input-group">
                  <input class="form-control" v-model="sufixo" v-on:keyup.enter="addSufixo(sufixo)" type="text" placeholder="Digite o Sufixo...">
                  <div class="input-group-append">
                    <button class="btn btn-info" v-on:click="addSufixo(sufixo)"><span class="fa fa-plus"></span></button>
                  </div>
                </div>

              </div>
            </div>
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
import "bootstrap/dist/css/bootstrap.css";
import "font-awesome/css/font-awesome.css";

export default {
	name: "app",
	data(){
		return{
			prefixo: "",
			sufixo: "",
			prefixos: ["Air","Jet","Flight"],
			sufixos:["Hub","Station","Mart"]
		};  
	},
	methods:{
		addSufixo(sufixo){
			if(sufixo != ""){
				this.sufixos.push(sufixo);
				this.sufixo = "";
			}
		},
		addPrefixo(prefixo){
			if(prefixo != ""){
				this.prefixos.push(prefixo);
				this.prefixo = "";
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
	}
};
</script>

<style>
  #slogan{
    margin: 30px 0px;
  }
  #main{
    background-color: #f1f1f1;
    padding: 30px 0px;
  }
</style>
