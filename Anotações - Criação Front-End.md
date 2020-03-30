#---Após o frontend pronto---#
@ Conectar o Front-End com o Back-End @

1º) Rodar o Start na pasta backend
2º) Instalar cliente axios http no frontend 
	- npm install axios
	- Arquivo api.js:
		import axios from 'axios';

		const api = axios.create({
			baseURL: 'http://localhost:3333'
		})

		export default api;
	
	- Importar esse arquivo dentro dos arquivos onde vão interagir com o Banco de Dados;
	
	
3º) Fazer a programação nos elementos para armazenar em variáveis e serem tratados do frontend para o backend:
	- const [name, setName] = useState(''); <- Adicionar o useState junto como React;
	- Adicionar os recursos do useState nos campos do formulário:
		<input placeholder="Nome da ONG" value={name} onChange={e => setName(e.target.value)}/>
	- Esse processo listado como exeplo, foi usado para cadastrar os registros no banco de dados:
		async function handleRegister(e){
			e.preventDefault();
		   
			const data = ({
				name,
				email,
				whatsapp,
				city,
				uf,
			});

			try{
				const response = await api.post('ongs', data);

				alert(`Seu ID de acesso: ${response.data.id}`);
				history.push('/');
			}catch (err){
				alert('Erro no cadastro, tente novamente.');
			}
		}
		
4º) Criar o sistema de Login:

	const [ id, setID ] = useState('');
    const history = useHistory();

    async function handleLogin(e){
        e.preventDefault();

        try{

            const response = await api.post('sessions', { id });
           localStorage.setItem('ongId', id);
           localStorage.setItem('ongName', response.data.name);
           history.push('/profile');

        }catch(err){
            alert('Falha no Login, tente novamente.');
        }
    }
	- O processo listado acima, serve para fazer login no sistema e armazer as informações no localStorage;
	
	
5º Listar todos os registros vinculados ao cadastro:
	- Será necessário o useEffect junto com o React e o useState;
	- Guardar as informações após o login:
		const ongName = localStorage.getItem('ongName');
	- usar o useEffect para mostrar as informações:
	----Inicio----
		const [ incidents, setIncidents ] = useState([]);
		const ongId = localStorage.getItem('ongId');
		const ongName = localStorage.getItem('ongName');

		useEffect(() => {
			api.get('profile', {
				headers: {
					Authorization: ongId,
				}
			}).then(response => {
				setIncidents(response.data);
			})
		}, [ongId]);
	----Exibição----
		<ul>
			{incidents.map(incident => (
				<li key={incident.id}>
					<strong>CASO: </strong>
					<p>{incident.title}</p>

					<strong>DESCRIÇÃO: </strong>
					<p>{incident.description}</p>

					<strong>VALOR: </strong>
					<p>{Intl.NumberFormat('pt-BR',{ style: 'currency', currency: 'BRL' }).format(incident.value)}</p>

					<button type="button">
						<FiTrash2 size={20} color="#a8a8b3" />
					</button>
				</li>
			))}
		</ul>
		
6º) Remover um registro:
	#----Inicio----#
	async function handleDeletIncident(id){
        try{
            await api.delete(`incidents/${id}`, {
                headers:{
                    Authorization: ongId,
                }
            });

            setIncidents(incidents.filter(incident => incident.id !== id));
        }catch (err){
            alert('Erro ao deletar caso, tente novamente.');
        }
    }
	#----Botão----#
	<button onClick={() => handleDeletIncident(incident.id)} type="button">
		<FiTrash2 size={20} color="#a8a8b3" />
	</button>
	
7º) Logout:
	- Necessário o useHistory;
	#----Inicio----#
	function handleLogout(){
        localStorage.clear();
        history.push('/')
    }
	Usar a function no botão de fazer logout
	
8º) Cadastrar novo registro:
	#----Início----#
	async function handleNewIncident(e){
        e.preventDefault();
        
        const data = {
            title,
            description,
            value,
        };

        try{
            await api.post('incidents', data, {
                headers: {
                    Authorization: ongId,
                }
            });
            
            alert(`Caso "${title}" cadastrado. Voltando para casos`);
            history.push('/profile');

        }catch(err){
            alert('Erro ao cadastrar caso, tente novamente.');
        }
    }
	#----Formulário----#
	<form onSubmit={handleNewIncident}>
		<input 
			placeholder="Titulo do caso"
			value={title}
			onChange={e => setTitle(e.target.value)}
		/>
		<textarea 
			placeholder="Descrição"
			value={description}
			onChange={e => setDescription(e.target.value)}
		/>
		<input 
			placeholder="Valor em Reais"
			value={value}
			onChange={e => setValue(e.target.value)}
		/>

		<button className="button" type="submit">Cadastrar</button>
	</form>
	
9º) 