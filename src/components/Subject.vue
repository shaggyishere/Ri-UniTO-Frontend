<template>
	<div class="column" @click="prenota()">
		<div class="box box-materia is-size-4">
			{{ materia }}
			<span
				v-if="editable"
				class="ml-2 has-text-right tag"
				v-bind:class="this.statusStyle()"
				>{{ this.statusText() }}</span
			>
			<div class="box-materia-professore">
				{{ professore }}
			</div>
			<div v-if="editable" class="mt-4 has-text-right">
				<!-- Inserire grafica modifiche -->
				<button
					@click="confermaEsecuzione"
					class="button is-success is-light mr-2"
					:disabled='isDisabled()'
				>
					Effettuata
				</button>
				<button
					@click="disdiciPrenotazione"
					class="button is-danger is-light"
					:disabled='isDisabled()'
				>
					Disdici
				</button>
			</div>
		</div>
	</div>
</template>
<script>
export default {
	name: "Subject",
	props: {
		id: Number,
		materia: String,
		professore: String,
		orario: String,
		data: String,
		statoPrenotazione: String,
		editable: {
			type: Boolean,
			default: false,
		},
	},
	methods: {
		test() {
			console.log(this.id);
		},
		statusStyle() {
			if (this.statoPrenotazione == "deleted") {
				return "is-warning";
			} else if (this.statoPrenotazione == "completed") {
				return "is-success";
			} else {
				return "is-link";
			}
		},
		statusText() {
			var testo = "";
			switch (this.statoPrenotazione) {
				case "deleted":
					testo = "Disdetta";
					break;
				case "completed":
					testo = "Effettuata";
					break;
				default:
					testo = "Prenotata";
					break;
			}
			return testo;
		},
		prenota() {
			if(!this.editable)
				this.$emit("pPrenota", {
				idPrenotazione: this.id,
				docente: this.professore,
				orario: this.orario,
				materia: this.materia,
				data: this.data
			});
		},
		confermaEsecuzione() {
			this.$emit("pEffettuata", {
				idPrenotazione: this.id,
				docente: this.professore,
				orario: this.orario,
				materia: this.materia,
				data: this.data,
			});
		},
		disdiciPrenotazione() {
			this.$emit("pDisdetta", {
				idPrenotazione: this.id,
				docente: this.professore,
				orario: this.orario,
				materia: this.materia,
				data: this.data,
			});
		},
		isDisabled(){
			if(this.statoPrenotazione == "deleted" || this.statoPrenotazione == "completed")
				return true;
			else 
				return false;
		}
	},
};
</script>

<style></style>
