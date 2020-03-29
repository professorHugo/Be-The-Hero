npm install express
npm install knex
npx knex init
	Configurações do Knexfile{
		development: {
			client: 'sqlite3',
			connection: {
			  filename: 'src/database/db.sqlite'
			},
			migrations:{
			  directory: './src/database/migrations'
			},
			useNullAsDefault: true,
		},
	}
	
npx knex migrate:make create_ongs <- Criar o arquivo de migração
	Exemplo de criação das tabelas no arquivo de migração:
		exports.up = function(knex) {
		  return knex.schema.createTable('ongs', function(table){
			  table.string('id').primary(),
			  table.string('name').notNullable(),
			  table.string('email').notNullable(),
			  table.string('whatsapp').notNullable(),
			  table.string('city').notNullable(),
			  table.string('uf',2).notNullable()
		  })
		};

		exports.down = function(knex) {
		  return knex.schema.dropTable('ongs');
		};
		
		
npx knex migrate:latest
	Fazer as migrações de todas as tabelas (será criado o arquivo db.sqlite
npx knex migrate:rollback
	Desfaz as migrações (apagar as tabelas: comando exports.down)
	
OBS: Caso tenha problemas com a migração, remover o arquivo db.sqlite e fazer a migração novamente com npx knex migrate:latest
	
