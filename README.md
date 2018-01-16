# Shri 2018. Задание 1. Старостин Евгений Валерьевич

## Найденные и исправленные ошибки

Задание «Комплексное» («Найди ошибки»). Исполнитель: Старостин Евгений Валерьевич

Список найденных и исправленных ошибок:

1) При вводе в терминал node ./index.js сервер не стартует (Dialect needs to be explicitly supplied as of v4.0.0)
Через отладчик, встроенный в Visual Studio Code удалось выяснить, что ошибка была в файле index.js каталога models в объявлении (const sequelize = new Sequelize(null, null, { ... });) 
Первым параметром должно идти название базы данных, к примеру const sequelize = new Sequelize('database', null, null, { ... });

2) Не открывается GraphQL IDE по ссылке http://localhost:3000/graphql/ (Cannot GET /graphql/) 
В файле index.js в корневой директории приложения в строке 14 опечатка, вместо буквы q буква g в слове graphql app.use('/graphgl', graphqlRoutes); 
Исправляем на app.use('/graphql', graphqlRoutes);

3) При попытке выполнить запрос {events {id}} ошибка "message": "argumets is not defined", 
В файле query.js в каталоге graphql/resolvers в строке 8 неизвестный аргумент 'argumets' 
return models.Event.findAll(argumets, context); Исправляем на return models.Event.findAll(args, context);

4) При выполнении запроса {rooms {id}} не выводится первая комната из-за заданного смещения в строке 20 в файле query.js в каталоге graphql/resolvers. 
return models.Room.findAll({ offset: 1 }, context); 
Убираем смещение return models.Room.findAll({}, context);

5) При выполнении запроса 
{events {id title dateStart dateEnd users {id login homeFloor avatarUrl} room {id title capacity floor}}} 
Не выводится список пользователей и комнат "users": null, "room": null 
В файле index.js каталога graphql/resolvers в строках 14 и 17 нет слова return.
Добавляем. Event: { users (event) { return event.getUsers(); }, room (event) { return event.getRoom(); } }

6) Для запроса mutation createUser в списке аргументов нет avatarUrl, 
чтобы была возможность добавить в БД путь к аватарке В файле typeDefs.js добавляем avatarUrl: String! 
input UserInput { login: String! homeFloor: Int avatarUrl: String! }

7) Для того, чтобы работал запрос addUserToEvent() нужно в файл mutation.js добавить 
addUserToEvent (root, { id, usersIds }, context) { return models.Event.findById(id) .then(event => { event.setUsers(usersIds); return event; }); }, 
И для того, чтобы можно было добавлять одним запросом нескольких пользователей в файле typeDefs.js изменим строку 
addUserToEvent(id: ID!, usersIds: [ID]!): Event 
И тогда запрос mutation {addUserToEvent(id:1, usersIds:[1,2,3]) {id}} прекрасно работает

8) Для корректной работы запроса changeEventRoom изменим в файле mutations.js id на roomid (event.setRoom(roomId)) 
changeEventRoom (root, { id, roomId }, context) { return models.Event.findById(id) .then(event => { event.setRoom(roomId); }); },

# shri-2018-task1
