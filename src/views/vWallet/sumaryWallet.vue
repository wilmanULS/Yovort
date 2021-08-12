<template>
<div>
	<h4 class="titleDivider pl-1 pt-3">Resumen vCoins</h4>
	<v-divider class="styleDivider"></v-divider>
	<v-container grid-list-xs class="no-padding">
		<v-layout row wrap>
			<v-flex xs12 md3 lg3>
				<v-card class="text-xs-center styleCards shadowCards">
					<v-btn fab dark :color="currentBussinesClub.theme.primary">
						<v-icon dark>fab fa-telegram-plane</v-icon>
					</v-btn>
					<v-flex>
						<span class="title-card">Enviar vCoins</span>
					</v-flex>
				</v-card>
			</v-flex>
			<v-flex xs12 md3 lg3 xl3>
				<v-card class="text-xs-center styleCards shadowCards">
					<v-btn fab dark :color="currentBussinesClub.theme.primary">
						<v-icon dark>fas fa-hand-holding-usd</v-icon>
					</v-btn>
					<v-flex>
						<span class="title-card">Solicitar vCoins</span>
					</v-flex>
				</v-card>
			</v-flex>
			<v-flex xs12 md3 lg3 xl3>
				<v-card class="text-xs-center styleCards shadowCards">
					<v-btn fab dark :color="currentBussinesClub.theme.primary">
						<v-icon dark>fas fa-comments-dollar</v-icon>
					</v-btn>
					<v-flex>
						<span class="title-card">Extraer vCoins</span>
					</v-flex>
				</v-card>
			</v-flex>
			<v-flex xs12 md3 lg3 xl3>
				<v-card class="text-xs-center styleCards shadowCards" style="border:1px solid black">
					<v-btn fab dark :color="currentBussinesClub.theme.primary">
						<v-icon dark>fas fa-money-check-alt</v-icon>
					</v-btn>
					<v-flex>
						<span class="title-card">Añadir vCoins</span>
					</v-flex>
				</v-card>
			</v-flex>
		</v-layout>
		<v-container grid-list-xs class="pt-5 ma-0 pl-0 pr-0">
			<v-layout row wrap>
				<v-flex xs12 md6 lg6>
					<v-card class="pl-3 pr-3">
						<v-layout row wrap class="pb-2">
							<v-flex xs8>
								<span class="title-coins">vCoins</span>
								<span class="subtitle-coins">Saldo Disponible</span>
							</v-flex>
							<v-flex xs4>
								<v-btn style="margin: 0px 35px; " outline :color="currentBussinesClub.theme.primary">
									<v-icon right dark>add</v-icon>
									Añadir
								</v-btn>
							</v-flex>
						</v-layout>
						<v-flex>
							<span class="subtitle-coins">$ </span>
							<span class="number-coins">8219</span>
							<span class="subtitle-coins">.96 </span>
							<v-icon color="green" small>fas fa-arrow-up</v-icon>
						</v-flex>
						<v-flex>
							<span class="subtitle-coins">+ 25% Comprando mes anterior</span>
						</v-flex>
						<v-divider class="styleDivider"></v-divider>
						<v-layout row wrap class="pb-2">
							<v-flex xs8>
								<span class="title-coins">Actividad</span>
								<span class="subtitle-coins">Hoy, 21/02/2019</span>
							</v-flex>
							<v-flex xs4>
								<v-select no-data-text="Datos no encontrados" outline :color="currentBussinesClub.theme.primary" :items="items" :value="items[0]"></v-select>
							</v-flex>
						</v-layout>
						<v-flex xs12>
							<area-chart :data="{'2017-01-01 00:00:00 -0800': 2, '2017-01-01 00:01:00 -0800': 5}"></area-chart>
						</v-flex>
						<v-card flat color="transparent" class="text-xs-center pb-2">
							<v-layout align-center justify-center row wrap>
								<v-flex xs1>
									<v-progress-circular :value="60" :color="currentBussinesClub.theme.primary"></v-progress-circular>
								</v-flex>
								<v-flex xs3>
									<span class="title-in-eg">Ingresos</span>
									<span class="subtitle-coins">+$23.00</span>
								</v-flex>
								<v-divider class="mx-3" inset vertical></v-divider>
								<v-flex xs1>
									<v-progress-circular :value="60" :color="currentBussinesClub.theme.primary"></v-progress-circular>
								</v-flex>
								<v-flex xs3>
									<span class="title-in-eg">Egresos</span>
									<span class="subtitle-coins">+$63.00</span>
								</v-flex>
							</v-layout>
						</v-card>

					</v-card>
				</v-flex>
				<v-flex xs12 md6 lg6>
				</v-flex>
			</v-layout>
		</v-container>
	</v-container>
</div>
</template>
<script>
import {
	mapGetters
} from "vuex";
export default {
	data() {
		return {
			items: ['Día', 'Mes', 'Año']

		}

	},
	computed: {
		...mapGetters([
			"userProfile",
			"genders",
			"countries",
			"provinceList",
			"prefixes",
			"currentBussinesClub",
		]),
		userProfile: {
			get() {
				return JSON.parse(this.$store.getters.userProfile);
			}
		}
	},

}
</script>
<style>
.styleDivider {
	margin-left: 5px;
	margin-right: 5px;
	padding-bottom: 2px;
}

.styleCards {
	height: 122% !important;
	width: 280px;
	border-radius: 15px
}

.title-in-eg {
	font-size: 18pt;
	font-weight: bold;
}

.title-card {
	font-size: 12pt;
	font-weight: bold;
}

.title-coins {
	font-size: 16pt;
	font-weight: bold;
}

.subtitle-coins {
	font-size: 10pt;
	color: gray;
	font-weight: bold;
}

.number-coins {
	font-size: 32pt;
	font-weight: 200;
}

.shadowCards {

	-moz-box-shadow: 2px 10px 28px 0px gray;

	-webkit-box-shadow: 2px 10px 28px 0px gray;

	box-shadow: 2px 10px 28px 0px gray;

	/* For IE 8 */

	-ms-filter: "progid:DXImageTransform.Microsoft.Shadow(Strength=4, Direction=135, Color='#000000')";

	/* For IE 5.5 - 7 */

	filter: progid:DXImageTransform.Microsoft.Shadow(Strength=4, Direction=135, Color='#000000');

}
</style>
