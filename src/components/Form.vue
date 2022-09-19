<script lang="ts">
	import { defineComponent } from 'vue';
	import Message from './Message.vue';

	interface Ingredient {
		id: number;
		tipo: string;
	}
	
	interface FormData {
		feedback: string;
		order: {
			customer: {
				name: string;
			}
			ingredient: {
				bread: string;
				beef: string;
				options: [];
			}
			status: "Solicitado" | "Em produção" | "Finalizado";
			message: string;
		}
		ingredients: {
			breads: Ingredient[],
			beefs: Ingredient[],
			options: Ingredient[],
		}
	}

	interface APIResponseIngredients {
		paes: Ingredient[];
		carnes: Ingredient[];
		opcionais: Ingredient[];
	}

	interface CreateOrder {
		customer: {
			name: string;
		}
		ingredient: {
			bread: string;
			beef: string;
			options: [];
		}
		status: "Solicitado" | "Em produção" | "Finalizado";
		message: string;
	}

	export default defineComponent({
    name: "Form",
	components: { Message },
    data(): FormData {
        return {
			feedback: "",
            order: {
                customer: {
                    name: ""
                },
                ingredient: {
                    bread: "",
                    beef: "",
                    options: [],
                },
                status: "Solicitado",
                message: ""
            },
            ingredients: {
                breads: [],
                beefs: [],
                options: [],
            },
        };
    },
    methods: {
        async getIngredients() {
            const response = await fetch("http://localhost:3000/ingredientes");
            if (response.ok) {
                const data = await response.json() as APIResponseIngredients;
                this.ingredients.breads = data.paes;
                this.ingredients.beefs = data.carnes;
                this.ingredients.options = data.opcionais;
            }
        },
        async createOrder(order: CreateOrder) {
            console.log(order);
            try {
                const response = await fetch("http://localhost:3000/pedidos", {
                    method: "POST",
                    body: JSON.stringify(order),
                    headers: { "Content-Type": "application/json" }
                });
                if (response.ok) {
                    const order = await response.json();
					this.handleFeedback(`
						<p>Pedido #${order.id} criado.</p>
						<p>O preparado já está em andamento.</p>
					`);
					setTimeout(() => this.handleFeedback(""), 5000);
                    this.resetModel();
                    await this.getOrders();
                    return;
                }
                throw new Error("não foi possível criar o pedido");
            }
            catch (error) {
                console.log(error);
            }
        },
        async getOrders() {
            try {
                const response = await fetch("http://localhost:3000/pedidos");
                if (response.ok) {
                    console.log(await response.json());
                }
            }
            catch (error) {
                console.log(error);
            }
        },
        handleSubmit(event: Event) {
            event.preventDefault();
            this.createOrder({ ...this.order });
        },
		handleFeedback(message: string) {
			this.feedback = message;
		},
        resetModel() {
            this.order.customer.name = "";
            this.order.ingredient.bread = "";
            this.order.ingredient.beef = "";
            this.order.ingredient.options = [];
        }
    },
    mounted() {
        // this.getIngredients();
    },
    beforeMount() {
        this.getIngredients();
    },
})

</script>

<template>
	<form class="form" @submit="handleSubmit">
		<div class="form__field">
			<label class="form__field-label" for="name">Nome</label>
			<input
				class="form__field-input"
				type="text"
				id="name"
				name="name"
				v-model="order.customer.name"
				placeholder="seu nome"
			>
		</div>
		<div class="form__field">
			<label class="form__field-label" for="bread">Pão</label>
			<select class="form__field-select" id="bread" name="bread" v-model="order.ingredient.bread">
				<option value="">escolha o pão</option>
				<option v-for="bread in ingredients.breads" :key="bread.id" :title="bread.tipo" :value="bread.tipo">{{ bread.tipo }}</option>
			</select>
		</div>
		<div class="form__field">
			<label class="form__field-label" for="beef">Carne</label>
			<select class="form__field-select" id="beef" name="beef" v-model="order.ingredient.beef">
				<option value="">escolha a carne</option>
				<option v-for="beef in ingredients.beefs" :key="beef.id" :title="beef.tipo" :value="beef.tipo">{{ beef.tipo }}</option>
			</select>
		</div>
		<div class="form__field">
			<label class="form__field-label" for="options">Opcionais</label>
			<ul class="form__field-options">
				<li class="form__field-option" v-for="option in ingredients.options" :key="option.id" :title="option.tipo">
					<input class="form__field-option-input" type="checkbox" name="opcionais" v-model="order.ingredient.options" :value="option.tipo">
					<span class="form__field-option-label">{{ option.tipo }}</span>
				</li>
			</ul>
		</div>
		<div class="form__container-button">
			<button type="submit" class="form__button">criar meu burguer</button>
		</div>
		<Message v-if="feedback" :message="feedback" />
	</form>
</template>

<style scoped>
	.form {
		max-width: 400px;
		width: 100%;
		margin: 0px auto 40px;
	}
	.form__field {
		display: flex;
		flex-direction: column;
		margin-bottom: 20px;
	}
	.form__field-label {
		padding: 5px 10px;
		border-left: 4px solid #fcba03;
		color: #222;
		margin-bottom: 15px;
		font-weight: bold;
	}

	.form__field-input, 
	.form__field-select {
		padding: 8px 10px;
		width: 100%;
		border-radius: 15px;
		height: 40px;
		outline: 0;
		border: 2px solid #fcba03;
		color: #222;
	}
	.form__field-input::placeholder {
		color: #222;
	}
	
	.form__field-options {
		list-style: none;
		padding: 0px;
		display: flex;
		flex-wrap: wrap;
		align-items: center;
		justify-content: flex-start;
	}
	.form__field-option {
		display: inline-flex;
		align-items: center;
		justify-content: flex-start;
		max-width: 50%;
		width: 100%;
		margin: 5px 0px;
	}
	.form__field-option-input {
		margin-right: 10px;
		width: 25px;
		height: 25px;
		border-radius: 50%;
		appearance: none;
		border: 2px solid #fcba03;
	}
	.form__field-option-input:checked {
		
		background-color: #fcba03;
	}
	.form__field-option-label {
		font-weight: bold;
		font-size: 14px;
	}
	.form__container-button {
		text-align: center;
		padding-top: 20px;
	}
	.form__button {
		border-radius: 15px;
		padding: 10px 40px;
		background-color: #222;
		color: #fcba03;
		cursor: pointer;
		transition: 0.3s ease-in;

		font-size: 18px;
		font-weight: bold;
	}
	.form__button:hover {
		background-color: transparent;
		color: #222;
	}
	
</style>