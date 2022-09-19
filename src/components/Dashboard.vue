<script lang="ts">
	import { defineComponent } from 'vue';
import Message from './Message.vue';

	interface DashboardData {
		feedback: string;
		orders: Order[];
		status: Status | [];
		orderId: number | null;
	}

	interface Order {
		id: number;
		customer: {
			name: string;
		}
		ingredient: {
			bread: string;
			beef: string;
			options: [];
		}
		status: OrderStatus;
		message: string;
	}

	type OrderStatus = "Solicitado" | "Em produção" | "Finalizado";

	type Status = [
			{
				id: number;
				tipo: "Solicitado"
			},
			{
				id: number;
				tipo: "Em produção"
			},
			{
				id: number;
				tipo: "Finalizado"
			},
		]

	export default defineComponent({
		name: "Dashboard",
		components: {
			Message
		},
		data(): DashboardData {
			return {
				feedback: "",
				orders: [],
				status: [],
				orderId: null
			};
		},
		methods: {
			async getOrders() {
				try {
					const response = await fetch("http://localhost:3000/pedidos");
					if (response.ok) {
						this.orders = await response.json();
					}
				}
				catch (error) {
					console.log(error);
					this.handleFeedback("Não foi possível buscar os pedidos da loja");
				}
			},
			async getStatus() {
				try {
					const response = await fetch("http://localhost:3000/status");
					if (response.ok) {
						this.status = await response.json();
					}
				}
				catch (error) {
					console.log(error);
					this.handleFeedback("Não foi possível buscar os status da loja");
					setTimeout(() => this.handleFeedback(''), 5000);
				}
			},
			async deleteOrder(id: number) {
				try {
					const response = await fetch("http://localhost:3000/pedidos/" + id, {
						method: "DELETE"
					});
					if (response.ok) {
						this.orders = this.orders.filter(order => order.id != id);
						this.handleFeedback(`Pedido #${id} excluído da loja`);
						setTimeout(() => this.handleFeedback(''), 5000);
					}
				}
				catch (error) {
					console.log(error);
					this.handleFeedback("Não foi possível excluir o pedido da loja");
					setTimeout(() => this.handleFeedback(''), 5000);
				}
			},
			async updateStatus(lastOrder: Order, newStatus: OrderStatus) {
				try {
					const response = await fetch("http://localhost:3000/pedidos/" + lastOrder.id, {
						method: "PUT",
						headers: {
							"Content-Type": "application/json"
						},
						body: JSON.stringify({
							...lastOrder,
							status: newStatus
						})
					});
					if (response.ok) {
						this.handleFeedback(`Pedido #${lastOrder.id} foi atualizado na loja`);
						setTimeout(() => this.handleFeedback(''), 5000);
						const matchOrder = this.orders.find(order => order.id == lastOrder.id);
						if(matchOrder) {
							matchOrder.status = newStatus;
						}
					}
				}
				catch (error) {
					console.log(error);
					this.handleFeedback("Não foi possível atualizar o pedido na loja");
					setTimeout(() => this.handleFeedback(''), 5000);
				}
			},
			handleFeedback(message: string) {
				this.feedback = message;
			},
			handleOrderCancel(id: number) {
				this.deleteOrder(id);
			},
			handleOrderStatus(event: Event, order: Order) {
				const currentElement = event.target as HTMLSelectElement;
				const orderStatus = currentElement.value as OrderStatus;
				this.updateStatus(order, orderStatus);
			}
		},
		beforeMount() {
			this.getOrders();
			this.getStatus();
		},
		
	})
</script>

<template>
	<section class="dashboard">

		<Message v-if="feedback" :message="feedback" />

		<table class="dashboard__orders" v-if="orders.length">
			<thead class="dashboard__headings">
				<th class="dashboard__heading dashboard__heading-number"><span>#</span></th>
				<th class="dashboard__heading"><span>Cliente</span></th>
				<th class="dashboard__heading"><span>Pão</span></th>
				<th class="dashboard__heading"><span>Carne</span></th>
				<th class="dashboard__heading"><span>Opcionais</span></th>
				<th class="dashboard__heading"><span>Ações</span></th>
			</thead>
			<tbody>
				<tr class="dashboard__order" v-for="order in orders" :key="order.id">
					<td v-if="order.status == 'Solicitado'" class="dashboard__order-number dashboard__order--solicitado">
						<span>{{ order.id }}</span>
					</td>
					<td v-else-if="order.status == 'Em produção'" class="dashboard__order-number dashboard__order--production">
						<span>{{ order.id }}</span>
					</td>
					<td v-else="order.status == 'Finalizado'" class="dashboard__order-number dashboard__order--finished">
						<span>{{ order.id }}</span>
					</td>
					<td class="dashboard__order-cliente"><span>{{ order.customer.name }}</span></td>
					<td class="dashboard__order-bread"><span>{{ order.ingredient.bread }}</span></td>
					<td class="dashboard__order-beef"><span>{{ order.ingredient.beef }}</span></td>
					<td class="dashboard__order-options">
						<ul>
							<li v-for="option in order.ingredient.options" :key="option" :title="option">{{ option }}</li>
						</ul>
					</td>
					<td class="dashboard__order-actions">
						<div class="dashboard__wrapper">
							<select class="dashboard__status" name="dashboard__status" id="dashboard__status" @change="handleOrderStatus($event, order)">
								<option disabled value="">Selecione o status</option>
								<template v-for="statu in status" :key="statu.id">
									<option v-if="statu.tipo == order.status" selected :value="statu.tipo" :title="statu.tipo">
										{{ statu.tipo }}
									</option>
									<option v-else :value="statu.tipo" :title="statu.tipo">
										{{ statu.tipo }}
									</option>
								</template>
							</select>
							<button class="dashboard__button" @click="handleOrderCancel(order.id)">cancelar</button>
						</div>
					</td>
				</tr>
			</tbody>
		</table>
		<Message v-else message="Nenhum pedido realizado na loja" />

	</section>
</template>

<style scoped>
	.dashboard {
		margin: 0px auto 30px;
	}
	.dashboard__orders {
		width: 100%;
		border-spacing: 0;
	}
	.dashboard__heading {
		border-bottom: 3px solid #222;
		padding: 10px 0px;
		text-align: left;
	}
	.dashboard__heading span {
		padding: 10px 60px 10px 0px;
	}
	
	.dashboard__heading-number span {
		padding: 10px 10px 10px 0px;
	}
	
	.dashboard__heading span {
		font-size: 16px;
		font-weight: bold;
	}
	.dashboard__order .dashboard__order-options ul {
		list-style: none;
		padding: 0px;
		display: flex;
		align-items: center;
		justify-content: flex-start;
	}
	.dashboard__order .dashboard__order-options li {
		margin-right: 10px;
	}
	.dashboard__order td {
		border-bottom: 1px solid #ccc;
	}
	.dashboard__order span {
		padding: 25px 0px;
		display: inline-block;
	}

	.dashboard__order-number span {
		padding: 25px 10px;
		font-weight: bold;
	}

	.dashboard__order-number.dashboard__order--solicitado span {
		background-color: rgb(255, 208, 0);
	}
	.dashboard__order-number.dashboard__order--production span {
		background-color: rgb(255, 136, 0);
	}
	.dashboard__order-number.dashboard__order--finished span {
		background-color: green;
	}

	.dashboard__order-actions .dashboard__wrapper {
		display: flex;
		align-items: center;
		justify-content: flex-start;
	}
	.dashboard__order-actions .dashboard__wrapper .dashboard__status {
		background-color: #222;
		color: #fcba03;
		border-radius: 5px;
		padding: 8px 20px;
		font-size: 14px;
		font-weight: bold;
		transition: 0.3s ease-in;
		margin-right: 10px;
	}
	.dashboard__order-actions .dashboard__wrapper .dashboard__button {
		background-color: #222;
		color: #fcba03;
		border-radius: 5px;
		padding: 8px 20px;
		font-size: 14px;
		font-weight: bold;
		transition: 0.3s ease-in;
	}
	.dashboard__order-actions .dashboard__wrapper .dashboard__button:hover {
		opacity: 0.8;
	}
</style>