<template>
	<div id="app">
		<div id="nav">
			<NavBar
				:autenticato="isLoggedIn"
				:accesso="showLoginForm"
				:disconnetti="doDisconnetti"
				:role="role"
			></NavBar>
			<ModalAccesso
				v-if="showLogin"
				:caricamento="caricamento"
				:toggle="toggleLoginForm"
				v-on:accedi-cliccato="doAccedi"
				:loginResponse="loginResponse"
			></ModalAccesso>
		</div>

		<router-view class="fullpage" :loginStatus="isLoggedIn" :accesso="showLoginForm" />

	</div>
</template>

<script>
import NavBar from "./components/NavBar";
import ModalAccesso from "./components/ModalAccesso";
import axios from "axios";
const qs = require("qs");

export default {
	data() {
		return {
			pagina: 0,
			role: "",
			titolo_settore: "Pagina web",
			message: "Hello Vue1!2",
			showLogin: false,
			isLoggedIn: false,
			caricamento: false,
			loginResponse: "",
		};
	},
	methods: {
		toggleLoginForm() {
			this.showLogin = !this.showLogin;
		},
		showLoginForm() {
			this.showLogin = true;
		},
		doAccedi(payload) {
			this.caricamento = true;

			var self = this;
			axios.defaults.withCredentials = true;
			axios
				.post(
					process.env.VUE_APP_SERVER_ADDRESS + "/log-in",
					qs.stringify({
						email: payload.email,
						password: payload.password,
					}),
					{
						headers: {
							"Content-Type": "application/x-www-form-urlencoded",
						},
					}
				)
				.then(function (response) {
					console.log(response);
					if (response.status == 200) {
						if (response.data.role) {
							if (response.data.role == "admin") {
								self.role = "admin";
							} else {
								self.role = "user";
							}
							console.log("ok");
							self.toggleLoginForm();
							self.isLoggedIn = true;
						} else
							self.loginResponse = "username o password errati";
					} else self.loginResponse = "Server irraggiungibile";
				})
				.catch(function (error) {
					self.loginResponse = "" + error;
					if('data' in error.response){
						if(error.response.data.includes('Nessun account trovato'))
							self.loginResponse = 'Username o password errati';
					}
				})
				.finally(function () {
					self.caricamento = false;
				});
		},
		doDisconnetti() {
			var self = this;
			axios.defaults.withCredentials = true;
			axios
				.delete(
					process.env.VUE_APP_SERVER_ADDRESS + "/log-in",
					{
						headers: {
							"Content-Type": "application/x-www-form-urlencoded",
						},
					}
				)
				.then(function (response) {
					console.log(response);
					if (response.status == 200) {
						console.log("sloggato");
						self.isLoggedIn = false;
						self.role="";
					} 
				}).finally(function () {
					self.$router.push('/');
				});
		},
	},
	components: {
		NavBar,
		ModalAccesso,
	},
};
</script>

<style>
@import "./css/mystyles.css";

fullpage {
  position: relative;
  min-height: 100vh;
  padding-bottom: 5rem;
}
</style>
