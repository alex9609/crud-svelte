<script>
  //Importar el toast
  import {Toast} from 'bootstrap';
  let toastEl;
  $: console.log(toastEl);

  let opc = {texto:'',color:''}

  let instancia;

  $: if(toastEl) {
    instancia = new Toast(toastEl)
  }

  const mostrarToast = (texto,color) => {
    opc = {texto,color}
    instancia.show()
  }

  let todos = [];
  let todo = {
    id: "",
    texto: "",
    estado: false,
  };

  //Esxiste algo llamado todos en local storages
  //El local storage solo mente se guarda en el navegador del cliente y solo vive en el dominio
  if (localStorage.getItem("todos")) {
    //Capturar los valores
    todos = JSON.parse(localStorage.getItem("todos"));
    //Guardar los valores
  }

  //Propideades computadas
  $: localStorage.setItem("todos", JSON.stringify(todos));
  const addTodo = () => {
    if (!todo.texto.trim()) {
      console.log("Texto vacio");
      todo.texto=''
      return;
    }
    // Le colocamos un numero de la fecha actual
    todo.id = Date.now();
    todos = [...todos, todo];
    console.log(todos);
    todo = {
      id: "",
      texto: "",
      estado: false,
    };
    mostrarToast('Todo agregado','bg-primary')
  };

  const delTodo = (id) => {
    todos = todos.filter((item) => item.id !== id);
    mostrarToast('Todo eliminado','bg-danger')
  };

  const editTodo = (id) => {
    todos = todos.map((item) =>
      item.id === id ? { ...item, estado: !item.estado } : item
    );
    mostrarToast('Todo actualizado','bg-warning')
    console.log(todos);
  };

  const classIcono = (valor) =>
    valor ? "bi bi-arrow-clockwise" : "bi bi-check";

  const classEstado = (valor) => (valor ? "btn-success" : "btn-warning");

  const realizado = (valor) => (valor ? "Hecho" : "Terminar");
</script>

<main>
  <div class="container">
    <h1 class="display-5 my-5 my-3">CRUD</h1>
    <!--preventDefault deja de procesar lo que hace el navegador por defector -->
    <form on:submit|preventDefault={addTodo}>
      <input
        type="text"
        placeholder="Enter para agregar todo"
        class="form-control shadow border-0"
        bind:value={todo.texto}
      />
    </form>
    {#each todos as todo}
      <div class="shadow my-3 p-3 lead">
        <p class={todo.estado ? "text-decoration-line-through" : ""}>
          {todo.texto}
        </p>
        <button
          class="btn btn-sm {classEstado(todo.estado)}"
          on:click={editTodo(todo.id)}
        >
          {realizado(todo.estado)}
          <i class={classIcono(todo.estado)} />
        </button>

        <button class="btn btn-sm btn-danger" on:click={delTodo(todo.id)}>
          Elimnar
          <i class="bi bi-trash" />
        </button>
      </div>
    {/each}
    
    <div class="toast-container position-abdolute p-3 top-0 end-0">
      <div bind:this={toastEl} class="toast align-items-center text-white {opc.color} border-0" role="alert" aria-live="assertive" aria-atomic="true">
        <div class="d-flex">
          <div class="toast-body">
            {opc.texto}
          </div>
          <button type="button" class="btn-close btn-close-white me-2 m-auto" data-bs-dismiss="toast" aria-label="Close"></button>
        </div>
      </div>
    </div>


  </div>
</main>

<style>
</style>
