# crudReact
Es una aplicación en React con las funciones básicas de CRUD, como backend utilizo una api en Java Sprint Boot.

Paso 1:

1. Crea un nuevo proyecto de React utilizando Create React App:
   npx create-react-app app_usuarios

2. Ingresa al directorio del proyecto:
   cd app_usuarios

Paso 2:

1. Crea los siguientes componentes en la carpeta src/components:
   UserList.js: Componente para mostrar la lista de usuarios.
   UserForm.js: Componente para agregar/editar usuarios.
   User.js: Componente para mostrar los detalles de un usuario específico.

Paso 3:

1. Instala React Router DOM para manejar las rutas en la aplicación:
   (Se utiliza esta versión para poder utilizar <Switch>)
   npm install react-router-dom@5.2.0
   npm install axios

3. Configura las rutas en src/App.js para cada componente creado en el Paso 2. El código es el siguiente:

   import React from 'react';
   import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
   import UserList from './components/UserList';
   import CreateUser from './components/CreateUser';
   import EditUser from './components/EditUser';
  
   function App() {
    return (
     <Router>
       <Switch>
         <Route path="/" exact component={UserList} />
         <Route path="/crear-usuario" component={CreateUser} />
         <Route path="/editar-usuario/:id" component={EditUser} />
       </Switch>
     </Router>
    );
   }
  
  export default App;


Paso 4:

1.Agregar el código que corresponde a cada componente:

  UserList.js - Componente para mostrar la lista de usuarios:

  import React, { useState, useEffect } from 'react';
  import { Link } from 'react-router-dom';
  import axios from 'axios';
  
  function UserList() {
    const [users, setUsers] = useState([]);
  
    useEffect(() => {
      const fetchUsers = async () => {
        try {
          const response = await axios.get('http://localhost:8080/api/users');
          setUsers(response.data);
        } catch (error) {
          console.error('Error al obtener la lista de usuarios:', error);
        }
      };
  
      fetchUsers();
    }, []);
  
      const crear = () => {
        window.location.href = '/crear-usuario';
      }
      const editar = async (id) => {
        window.location.href = `/editar-usuario/${id}`;
      }
  
  
    const handleDelete = async (userId) => {
      try {
        await axios.delete(`http://localhost:8080/api/users/${userId}`);
        setUsers(users.filter(user => user.id !== userId));
        alert('Usuario eliminado exitosamente');
      } catch (error) {
        console.error('Error al eliminar el usuario:', error);
        alert('Error al eliminar el usuario');
      }
    };
  
    return (
      <div>
        <h1>Lista de Usuarios</h1>
        <Link to="/crear-usuario">
          <button className={"button"} onClick={crear}>Crear Nuevo Usuario</button>
        </Link>
        {users.map(user => (
          <div key={user.id}>
            <p>Nombre: {user.nombre}</p>
            <p>Apellido: {user.apellido}</p>
            <p>Email: {user.email}</p>
            <button onClick={() => handleDelete(user.id)}>Eliminar</button>
            <Link to={`/editar-usuario/${user.id}`}>
              <button onClick={() => editar(user.id)}>Editar</button>
            </Link>
          </div>
        ))}
      </div>
    );
  }
  
  export default UserList;

  CreateUser.js - Componente para agregar/editar usuarios:

  import React, { useState } from 'react';
  import axios from 'axios';
  
  function CreateUser() {
    const [formData, setFormData] = useState({
      nombre: '',
      apellido: '',
      email: ''
    });
  
    const home = () => {
      window.location.href = '/';
    }
    const handleChange = (e) => {
      setFormData({ ...formData, [e.target.name]: e.target.value });
    };
  
    const handleSubmit = async (e) => {
      e.preventDefault();
      try {
        await axios.post('http://localhost:8080/api/users', formData);
        alert('Usuario creado exitosamente');
        setFormData({ nombre: '', apellido: '', email: '' });
        window.location.href = '/';
      } catch (error) {
        console.error('Error al crear el usuario:', error);
        alert('Error al crear el usuario');
      }
    };
  
    return (
      <div>
        <h1>Crear Nuevo Usuario</h1>
          <button className={"button"} onClick={home}>Home</button>
        <form onSubmit={handleSubmit}>
          <div>
            <label>Nombre:</label>
            <input type="text" name="nombre" value={formData.nombre} onChange={handleChange} />
          </div>
          <div>
            <label>Apellido:</label>
            <input type="text" name="apellido" value={formData.apellido} onChange={handleChange} />
          </div>
          <div>
            <label>Email:</label>
            <input type="email" name="email" value={formData.email} onChange={handleChange} />
          </div>
          <button type="submit">Crear Usuario</button>
        </form>
      </div>
    );
  }
  
  export default CreateUser;


  EditUser.js - Componente para mostrar los detalles de un usuario específico:

  import React, { useState, useEffect } from 'react';
  import { useParams } from 'react-router-dom';
  import axios from 'axios';
  
  function EditUser() {
    const { id } = useParams();
    const [user, setUser] = useState({ nombre: '', apellido: '', email: '' });
  
    useEffect(() => {
      const fetchUser = async () => {
        try {
          const response = await axios.get(`http://localhost:8080/api/users/${id}`);
          setUser(response.data);
        } catch (error) {
          console.error('Error al obtener el usuario:', error);
        }
      };
  
      fetchUser();
    }, [id]);
  
    const home = () => {
      window.location.href = '/';
    }
    const handleChange = (e) => {
      setUser({ ...user, [e.target.name]: e.target.value });
    };
  
    const handleSubmit = async (e) => {
      e.preventDefault();
      try {
        await axios.put(`http://localhost:8080/api/users/${id}`, user);
        alert('Usuario actualizado exitosamente');
        window.location.href = '/';
      } catch (error) {
        console.error('Error al actualizar el usuario:', error);
        alert('Error al actualizar el usuario');
      }
    };
  
    return (
      <div>
        <h1>Editar Usuario</h1>
          <button className={"button"} onClick={home}>Home</button>
        <form onSubmit={handleSubmit}>
          <div>
            <label>Nombre:</label>
            <input type="text" name="nombre" value={user.nombre} onChange={handleChange} />
          </div>
          <div>
            <label>Apellido:</label>
            <input type="text" name="apellido" value={user.apellido} onChange={handleChange} />
          </div>
          <div>
            <label>Email:</label>
            <input type="email" name="email" value={user.email} onChange={handleChange} />
          </div>
          <button type="submit">Actualizar Usuario</button>
        </form>
      </div>
    );
  }
  
  export default EditUser;

Paso 5:

1. Probamos correr la app:
   npm start

Listo, agrego la carpeta con la api en java.

