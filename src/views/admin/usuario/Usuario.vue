<template>
    <div>
        <h1>Gestión Usuarios</h1>
        <form @submit.prevent="guardarUsuario">
            <label for="n">Ingrese Nombre:</label>
            <input type="text" id="n" v-model="usuario.name">
            <br>
            <label for="c">Ingrese Correo:</label>
            <input type="email" id="c" v-model="usuario.email">
            <br>
            <label for="cc">Ingrese Contraseña:</label>
            <input type="password" id="cc" v-model="usuario.password">
            <br>
            <input type="submit">            
        </form>

        <input type="text" @keyup.enter="buscarUsuarios" v-model="buscar" placeholder="buscar...">
        <table border="1">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>NOMBRE</th>
                    <th>CORREO</th>
                    <th>DATOS PERSONALES</th>
                    <th>ACCIONES</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="usuario in usuarios" :key="usuario.id">
                    <td>{{ usuario.id }}</td>
                    <td>{{ usuario.name }}</td>
                    <td>{{ usuario.email }}</td>
                    <td>
                        <div v-if="usuario.persona">
                            {{ usuario.persona?.nombre_completo }} - {{ usuario.persona?.apellidos }}
                        </div>
                        <div v-else>
                            <button>Datos Personales</button>
                        </div>
                    </td>
                    <td>
                        <button @click="editarUsuario(usuario)">editar</button>
                        <button @click="eliminarUsuario(usuario.id)">eliminar</button>
                    </td>
                </tr>
            </tbody>
        </table>

    </div>
</template>
<script setup>
// importaciones
    import { onMounted, ref } from "vue"
    import usuarioService from "./../../../services/usuario.service"
// declaracion de variables
    const usuarios = ref([]);
    const usuario = ref({});
    const buscar = ref("")
// ciclo de vida del comopnente
    onMounted(() => {
        listaUsuarios()
    })
// metodos
    const listaUsuarios = async () =>{
        try {
            const { data } = await usuarioService.listar();
    
            usuarios.value = data;
            
        } catch (error) {
            console.log(error);
            // alert("Error al recuperar los datos de usuarios");
        }
    }

    const guardarUsuario = async () => {
        try {
            if(usuario.value.id){
                // modificar
                await usuarioService.modificar(usuario.value.id, usuario.value);

            }else{
                await usuarioService.guardar(usuario.value);
            }
            usuario.value = {}
            listaUsuarios()
        } catch (error) {
            alert("Error al registrar el usuario")
        }
    }

    const editarUsuario = (data) => {
        usuario.value = data;
    }

    const buscarUsuarios = async () => {
        const {data} = await usuarioService.listar(buscar.value);
        usuarios.value = data
        
    }

    const eliminarUsuario = async (id) => {
        if(confirm("Está seguro de eliminar al usuario?")){

            const {data} = await usuarioService.eliminar(id)
            listaUsuarios()
        }

    }

</script>