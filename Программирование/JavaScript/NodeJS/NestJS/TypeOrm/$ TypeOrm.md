# Содержание:
[[JoinColumn и JoinTable]]
# Введение
Для создания и управления записями в БД через TypeScript можно использовать Orm библиотеку, например TypeOrm.
# Установка
Для NestJS есть специальная версия этой библиотеки для более удобной работы. Для начала необходимо ее установить:
```tsx
yarn add @nestjs/typeorm typeorm mysql2 --dev
```

Также нам понадобится ConfigService для взаимодействия с .env файлом:
```tsx
yarn add @nestjs/config
```

Для создания DTO (о них позже в этом конспекте) необходима библиотека:
```tsx
yarn add class-validator
```
## Конфигурация и подключение

> В данном конспекте не рассматривается установка, создание и запуск БД. Все это будет рассмотрено в конспекте по Docker.

Создадим файл .env для хранения конфигурационных значений:
```tsx
MYSQL_HOST = 127.0.0.1
MYSQL_PORT = 3306
MYSQL_DATABASE = 'nestjs_tasks'
MYSQL_USERNAME = 'root'
MYSQL_ROOT_PASSWORD = 'root'
```

Далее необходимо создать модуль для конфигурации базы данных:
```tsx
nest g mo database
```

Далее импортируем TypeOrmModule и передаем в него параметры из ConfigService:
```tsx
import { Module } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRootAsync({
      useFactory: (configService: ConfigService) => ({
        type: 'mysql',
        host: configService.getOrThrow('MYSQL_HOST'),
        port: configService.getOrThrow('MYSQL_PORT'),
        password: configService.getOrThrow('MYSQL_ROOT_PASSWORD'),
        username: configService.getOrThrow('MYSQL_USERNAME'),
        database: configService.getOrThrow('MYSQL_DATABASE'),
        autoLoadEntities: true,
        synchronize: true,
      }),
      inject: [ConfigService],
    }),
  ],
})
export class DatabaseModule {}

```

Теперь необходимо дополнить главный модуль app.module.ts:
```tsx
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { DatabaseModule } from './database/database.module';
import { ConfigModule } from '@nestjs/config';
import { UserModule } from './user/user.module';

@Module({
  imports: [ConfigModule.forRoot({ isGlobal: true }), DatabaseModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```
После этого, можно запустить программу и проверить соединение с БД.
## Создание таблиц и записей
Для создание таблицы, необходимо создать ресурс того, что мы будем хранить. Для примера создадим ресурс user:
```tsx
nest g res user
```

При создании ресурса появятся 2 вопроса: Какой тип архитектуры использовать, выбираем REST API и нужно ли создавать CRUD, выбираем Yes.
После этого будет создана папка src/user:
![[Untitled 1.png]]
DTO (Data Transfer Object) - это объекты для верификации данных, которые передаются в теле запросов. По умолчанию создаются пустые DTO для создания и обновления.

Entities (Сущности) представляют собой таблицы, в которых будет хранится информация.

Заполним user.entity.ts:
```tsx
import { Column, Entity, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: number;

  @Column()
  name: string;

  @Column()
  password: string;
}

```

Сразу же заполним create-user.dto.ts:
```tsx
import { IsString } from 'class-validator';

export class CreateUserDto {
  @IsString()
  readonly name: string;

  @IsString()
  readonly password: string;
}

```

Далее необходимо добавить сущность в TypeOrmModule, для этого в файле user.module.ts добавим импорт:
```tsx
import { Module } from '@nestjs/common';
import { UserService } from './user.service';
import { UserController } from './user.controller';
import { TypeOrmModule } from '@nestjs/typeorm';
import { User } from './entities/user.entity';

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UserController],
  providers: [UserService],
})
export class UserModule {}

```

По умолчанию Nest создает дурацкие методы для сервиса, поэтому надо их переписать:
```tsx
import { Injectable } from '@nestjs/common';
import { CreateUserDto } from './dto/create-user.dto';
import { InjectRepository } from '@nestjs/typeorm';
import { User } from './entities/user.entity';
import { Repository } from 'typeorm';

@Injectable()
export class UserService {
  constructor(
    @InjectRepository(User) // Подключение репозитория с сущноситью User
    private userRepository: Repository<User>,
  ) {}

  create(createUserDto: CreateUserDto) {
    return this.userRepository.save(createUserDto);
  }

  findAll() {
    return this.userRepository.find();
  }
}

```

Соответственно обновим и контроллер:
```tsx
import { Controller, Get, Post, Body } from '@nestjs/common';
import { UserService } from './user.service';
import { CreateUserDto } from './dto/create-user.dto';

@Controller('user')
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Post()
  create(@Body() createUserDto: CreateUserDto) {
    return this.userService.create(createUserDto);
  }

  @Get()
  findAll() {
    return this.userService.findAll();
  }
}

```