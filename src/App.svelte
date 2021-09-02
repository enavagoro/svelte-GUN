<script>
	import Gun from "gun/gun";

	const gun = Gun().get("todos"); //to-do 
	
	// Crea un local store para guardar la data de GUN.
	let store = {}
	
	//console.log('gun:',gun);

	// Crea un listener que itera sobre las keys encontradas en el nodo "todos" que definimos en la linea 4 en GUN.
	gun.map().on(function(todo, key) {
		if (todo) {
			// Actualiza el store con un nuevo value (una tabla de hash,(index)).
			store[key] = todo
		} else {
			//Si todo no existe significa que la data fue borrada o seteada como null.

 			// en ese caso borramos el item del objeto store :V
			delete store[key]
			store = store
		}
	})
	
	//Si cambia el objeto store actualizan.
	//Estas variables están echas para interactuar con el html.
	$: todos = Object.entries(store);
	$: done = todos.filter(([key, todo]) => todo.done).length
	
	// Acciones que escriben data en GUN.
	const create = key => gun.set(key).put({ title: key, done: false });
	const update = (key, value) => gun.get(key).get("done").put(value);
	const remove = key => gun.get(key).put(null);
</script>
							   <!-- Cuando cambia activa el crear -->
<input placeholder="add to do" on:change={e => create(e.target.value) && (e.target.value = "")} />

<h1> Completed {done} / {todos.length} </h1>

{#each todos as [key, todo]}
	<div id={key}>
		<input type="checkbox" checked={todo.done} on:change={() => update(key, !todo.done)} />
		{todo.title} <a href="/" on:click|preventDefault={() => remove(key)}>remove</a> {todo.done ? "😺" : "😾"}
	</div>
{/each}