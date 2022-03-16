<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo_text.svg" width="320" alt="Nest Logo" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

  <p align="center">A progressive <a href="http://nodejs.org" target="_blank">Node.js</a> framework for building efficient and scalable server-side applications.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://opencollective.com/nest#backer" target="_blank"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor" target="_blank"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
  <a href="https://paypal.me/kamilmysliwiec" target="_blank"><img src="https://img.shields.io/badge/Donate-PayPal-ff3f59.svg"/></a>
    <a href="https://opencollective.com/nest#sponsor"  target="_blank"><img src="https://img.shields.io/badge/Support%20us-Open%20Collective-41B883.svg" alt="Support us"></a>
  <a href="https://twitter.com/nestframework" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

## Description

[Nest](https://github.com/nestjs/nest) framework TypeScript starter repository.

## Installation

```bash
$ npm install
```

## Running the app

```bash
# development
$ npm run start

# watch mode
$ npm run start:dev

# production mode
$ npm run start:prod
```

## Test

```bash
# unit tests
$ npm run test

# e2e tests
$ npm run test:e2e

# test coverage
$ npm run test:cov
```

## Help

```bash
npm install -g @nestjs/cli
```

```bash
nest new nest-shopping-list
```

```bash
cd nest-shopping-list
```

```bash
npm install --save @nestjs/typeorm typeorm mysql2 @nestjs/config class-validator class-transformer @nestjs/swagger swagger-ui-express
```

```bash
nest generate resource item
```

File ".env"

```
SERVER_PORT=3000
MODE=DEV
DB_HOST=127.0.0.1
DB_PORT=3306
DB_USERNAME=meet_pet_dbo
DB_PASSWORD=P@ssw0rd
DB_DATABASE=meet_pet
DB_SYNCHRONIZE=true
```

File "src/app.module.ts"

```javascript
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config'; //add this line
import { TypeOrmModule } from '@nestjs/typeorm'; //add this line
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ItemModule } from './item/item.module';

@Module({
  imports: [
    ConfigModule.forRoot(), //add this line
    TypeOrmModule.forRoot({ //add this block
      type: 'mysql',
      host: process.env.DB_HOST,
      port: parseInt(process.env.DB_PORT),
      username: process.env.DB_USERNAME,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_DATABASE,
      entities: [__dirname + '/**/*.entity{.ts,.js}'],
      synchronize: (process.env.DB_SYNCHRONIZE === 'true'),
    }),
    ItemModule
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule { }
```

File "src/item/entities/item.entity.ts"

```javascript
import { BaseEntity, Column, Entity, PrimaryGeneratedColumn, UpdateDateColumn } from "typeorm";  //add this line

@Entity() //add this line
export class Item extends BaseEntity { //add this block
    @PrimaryGeneratedColumn('uuid')
    id: string;

    @UpdateDateColumn({ name: 'updated_at', type: 'timestamp' })
    updatedAt: Date;

    @Column({ name: 'name', type: 'varchar', length: 50 })
    name: string;

    @Column({ name: 'description', type: 'varchar', nullable: true, length: 255 })
    description?: string;

    @Column({ name: 'quantity', type: 'int' })
    quantity: number;
}
```

File "src/item/dto/create-item.dto.ts"

```javascript
import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';  //add this line
import { IsInt, IsNotEmpty, IsOptional, IsString, Min } from 'class-validator';  //add this line

export class CreateItemDto { //add this block
    @ApiProperty({ example: 'Bananas' })
    @IsString()
    @IsNotEmpty()
    name: string;

    @ApiPropertyOptional({ example: 'Cavendish bananas', description: 'Optional description of the item' })
    @IsOptional()
    @IsString()
    description: string;

    @ApiProperty({ example: 5, description: 'Needed quantity' })
    @IsInt()
    @Min(0)
    quantity: number;
}
```

File "src/main.ts"

```javascript
import { ValidationPipe } from '@nestjs/common'; //add this line
import { NestFactory } from '@nestjs/core';
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger'; //add this line
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(new ValidationPipe()); //add this line

  const config = new DocumentBuilder() //add this line
    .setTitle('Shopping list API') //add this line
    .setDescription('My shopping list API description') //add this line
    .setVersion('1.0') //add this line
    .build(); //add this line

  SwaggerModule.setup('api', app, SwaggerModule.createDocument(app, config)); //add this line

  await app.listen(3000);
}

bootstrap();
```

File "src/item/item.service.ts"

```javascript
import { Injectable, NotFoundException } from '@nestjs/common'; //edit this line
import { InjectRepository } from '@nestjs/typeorm'; //add this line
import { Repository } from 'typeorm'; //add this line
import { CreateItemDto } from './dto/create-item.dto';
import { UpdateItemDto } from './dto/update-item.dto';
import { Item } from './entities/item.entity'; //add this line

@Injectable()
export class ItemService {
  constructor(@InjectRepository(Item) private readonly repository: Repository<Item>) { } //add this line

  create(createItemDto: CreateItemDto): Promise<Item> { //edit this line
    const item = this.repository.create(createItemDto); //add this line
    return this.repository.save(item); //edit this line
  }

  findAll(): Promise<Item[]> { //edit this line
    return this.repository.find(); //edit this line
  }

  findOne(id: string): Promise<Item> { //edit this line
    return this.repository.findOne(id); //edit this line
  }

  async update(id: string, updateItemDto: UpdateItemDto): Promise<Item> { //edit this line
    const item = await this.repository.preload({ //add this line
      id: id, //add this line
      ...updateItemDto, //add this line
    }); //add this line
    if (!item) { //add this line
      throw new NotFoundException(`Item ${id} not found`); //add this line
    } //add this line
    return this.repository.save(item); //edit this line
  }

  async remove(id: string) { //edit this line
    const item = await this.findOne(id); //add this line
    return this.repository.remove(item); //edit this line
  }
}
```

File "src/item/item.module.ts"

```javascript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm'; //add this line
import { Item } from './entities/item.entity'; //add this line
import { ItemService } from './item.service';
import { ItemController } from './item.controller';

@Module({
  imports: [TypeOrmModule.forFeature([Item])], //add this line
  controllers: [ItemController],
  providers: [ItemService]
})
export class ItemModule { }
```

Reference: https://www.sidechannel.blog/en/creating-an-api-with-nestjs/index.html

## Support

Nest is an MIT-licensed open source project. It can grow thanks to the sponsors and support by the amazing backers. If you'd like to join them, please [read more here](https://docs.nestjs.com/support).

## Stay in touch

- Author - [Kamil Myśliwiec](https://kamilmysliwiec.com)
- Website - [https://nestjs.com](https://nestjs.com/)
- Twitter - [@nestframework](https://twitter.com/nestframework)

## License

Nest is [MIT licensed](LICENSE).
