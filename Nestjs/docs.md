## REQUEST

- Trong NestJS, quá trình tiếp nhận request được điều khiển bởi middleware và interceptor. Dưới đây là thứ tự cơ bản mà một request đi qua trong một ứng dụng NestJS:

1. Middleware Global: Những middleware được đăng ký toàn cục (global middleware) sẽ được áp dụng cho mọi request vào ứng dụng. Đây là nơi lý tưởng để xử lý các vấn đề như xác thực, log, hoặc xử lý headers.
2. Middleware Cấp Ứng Dụng (Application-level Middleware): Những middleware được đặt ở cấp ứng dụng (application-level middleware) áp dụng cho một nhóm các route hoặc controller cụ thể.
3. Guard (AuthGuard): Nếu một route hoặc controller có sử dụng Guard, NestJS sẽ gọi đến Guard để kiểm tra điều kiện xác thực trước khi request tiếp tục. Guard có thể dừng request nếu điều kiện không đúng.
4. Interceptor (Before Interceptor): Nếu một interceptor được đặt trước action (before interceptor) cho một route hoặc controller, NestJS sẽ gọi interceptor này. Interceptor có thể thay đổi request hoặc thêm thông tin vào request trước khi nó đến tay controller.
5. Pipe (Validation Pipe): Nếu một pipe (như Validation Pipe) được kích hoạt, NestJS sẽ sử dụng nó để xác thực và chuyển đổi dữ liệu từ request body. Pipe có thể từ chối request nếu dữ liệu không hợp lệ.
6. Controller Handler (Action Method): Request sẽ được chuyển đến action method trong controller tương ứng. Đây là nơi xử lý chính cho request, thực hiện logic và trả về kết quả.
7. Interceptor (After Interceptor): Sau khi controller handler hoàn thành, NestJS có thể gọi interceptor sau action (after interceptor). Interceptor này có thể thay đổi response hoặc xử lý bất kỳ logic nào sau khi action method hoàn tất.
8. Exception Filters: Nếu một exception xảy ra trong quá trình xử lý request, exception filter sẽ được gọi để xử lý exception và trả về một response lỗi thích hợp.
9. Response

## DECORATOR

-Trong NestJS, "decorator" là một tính năng của TypeScript được sử dụng để thêm metadata vào các lớp, phương thức, thuộc tính hoặc tham số. Decorator giúp xác định cách các thành phần trong ứng dụng NestJS hoạt động và tương tác với nhau
một số decorator cơ bản trong nestjs

1.  @Moduel() : được sử dùng dể định nghĩa là 1 module baoif gồm các providers ,controller và các moudel khác mà mudule này phụ thuộc

```ts
@Module({
  imports: [OtherModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

2.@controller(): được sử dùng để định nghĩa 1 controller, sử lý các yêu câu HTTP đến.

```ts
@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
```

3. @Injectable(): được sử dùng dể đánh dấu một class là một provider có thể inject vào các thành phần khác

```ts
@Injectable()
export class CatsService {
  getCats(): string {
    return 'All cats';
  }
}
```

4. các phước thức @Get,@post...., @Param
5. @userGuards(): được áp dựng cá guards (bảo vệ) cho route hoạc controller

- @UseGuards là một decorator trong NestJS được sử dụng để áp dụng các guards cho một route hoặc một controller. Guards trong NestJS là các lớp có thể triển khai giao diện CanActivate, được sử dụng để quyết định liệu một yêu cầu có được xử lý bởi route đó hay không. Guards thường được sử dụng cho mục đích xác thực và ủy quyền.
  - Cách hoạt động của Guards
    -> Guards được thực thi trước khi các middleware, interceptors, pipes và các handler của route. Khi một guard được áp dụng, phương thức canActivate của guard đó sẽ được gọi và nó sẽ quyết định liệu yêu cầu có được tiếp tục hay không dựa trên một boolean hoặc một promise mà kết quả là boolean.

## S0LID

s - > single responsibility principle
-> mỗi class chi chiệu tránh nhiếm cho 1 chứ năng duy nhất

controller -> service -> respositoty

controller -> response data
service -> handle business logic
repository -> handle logic related to data

decoretor : @GET @POST

## Dependency Injection

->dependency injection là một kỹ thuật theo đó một đối tượng (hoặc static method) cung cấp các phụ thuộc của đối tượng khác. Một phụ thuộc là một đối tượng có thể được sử dụng (service)

- Cách hoạt động của DI Container trong NestJS
  1 . Providers: Trong NestJS, các class hoặc giá trị có thể được đánh dấu là providers bằng cách sử dụng decorator @Injectable(). Những providers này có thể được inject vào các class khác thông qua constructor.

```ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class CatsService {
  findAll(): string {
    return 'This action returns all cats';
  }
}
```

2.  Injection: Các providers có thể được inject vào các class khác thông qua constructor. NestJS DI container sẽ tự động giải quyết và cung cấp các phụ thuộc cần thiết.

```ts
import { Controller, Get } from '@nestjs/common';
import { CatsService } from './cats.service';

@Controller('cats')
export class CatsController {
  constructor(private readonly catsService: CatsService) {}

  @Get()
  findAll(): string {
    return this.catsService.findAll();
  }
}
```

3.  Modules: Modules là các đơn vị logic để tổ chức các providers và controllers trong ứng dụng. Mỗi module có thể khai báo các providers mà nó quản lý và các providers đó sẽ có phạm vi trong module đó.

```ts
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}
```

4.  Global Providers: Nếu bạn muốn một provider có phạm vi toàn cục và có thể được inject vào bất kỳ module nào, bạn có thể sử dụng decorator @Global() hoặc đăng ký provider tại module gốc (root module).

- nestjs cùng cấp DI container để lưu service và respository
  Di CONTAINER [
  service,
  respository
  ]
  trong nestjs

---

cách nestjs lấy service để quản lý

- đánh dấu decorator cho service -> đưa service vào provider của module

---

các controller sữa sùng service tử Di conttainer

- dùng constructor để inject service

## MODULE

- tạo moduel npx nest g mo <name>
- tạo thủ công
  - tạo folder chứa model
  - tên file <name>.module.ts
  - sửa dụng decorator @Module để đánh dấu là 1 module
    decorate MODULE trong nestjs

## Pipes

-> Trong NestJS, pipes là một thành phần quan trọng được sử dụng để xử lý dữ liệu đầu vào của các controller trước khi dữ liệu này được chuyển tới các handler hoặc method xử lý request. Pipes giúp kiểm tra, biến đổi và làm sạch dữ liệu trước khi nó được sử dụng bởi các phương thức xử lý trong NestJS.

#### Các Đặc Điểm Của Pipes Trong NestJS

1.  Validation (Kiểm Tra): Pipes có thể được sử dụng để kiểm tra tính hợp lệ của dữ liệu đầu vào bằng cách sử dụng decorators từ thư viện class-validator.
2.  Transformation (Biến Đổi): Pipes có thể biến đổi dữ liệu đầu vào thành định dạng phù hợp trước khi nó được chuyển tới handler.
3.  Error Handling (Xử Lý Lỗi): Pipes cũng có thể xử lý các lỗi phát sinh trong quá trình kiểm tra và biến đổi dữ liệu, cho phép bạn điều hướng và xử lý lỗi một cách linh hoạt.
4.  Custom Pipes (Pipes Tự Định Nghĩa): Bạn có thể tự định nghĩa các pipes để xử lý logic đặc biệt cho ứng dụng của mình, đảm bảo tính tái sử dụng và tổ chức mã nguồn hiệu quả.

#### Cách Sử Dụng Pipes Trong NestJS

1.  Tạo Một Custom Pipe: Định nghĩa một pipe mới bằng cách tạo một class implement PipeTransform từ @nestjs/common.

```ts
import {
  Injectable,
  PipeTransform,
  ArgumentMetadata,
  BadRequestException,
} from '@nestjs/common';

@Injectable()
export class ParseIntPipe implements PipeTransform<string, number> {
  transform(value: string, metadata: ArgumentMetadata): number {
    const val = parseInt(value, 10);
    if (isNaN(val)) {
      throw new BadRequestException('Validation failed: Integer is expected.');
    }
    return val;
  }
}
```

2.  Áp Dụng Pipe Trên Controller Hoặc Phương Thức: Sử dụng decorator @UsePipes để áp dụng pipe lên một controller hoặc phương thức cụ thể.

- Áp dụng global pipe: Thêm vào AppModule để áp dụng cho tất cả các controller

```ts
import { Module } from '@nestjs/common';
import { APP_PIPE } from '@nestjs/core';
import { ParseIntPipe } from './parse-int.pipe';

@Module({
  providers: [
    {
      provide: APP_PIPE,
      useClass: ParseIntPipe,
    },
  ],
})
export class AppModule {}
```

- Áp dụng local pipe: Sử dụng decorator @UsePipes trên controller hoặc phương thức trong controller.

```ts
import { Controller, Get, Post, Body, UsePipes } from '@nestjs/common';
import { ParseIntPipe } from './parse-int.pipe';

@Controller('cats')
@UsePipes(new ParseIntPipe())
export class CatsController {
  @Get()
  findAll(@Query('page') page: number) {
    return `This action returns all cats (page: ${page})`;
  }

  @Post()
  @UsePipes(new ParseIntPipe())
  create(@Body() createCatDto: any) {
    return `This action creates a new cat with name: ${createCatDto.name}`;
  }
}
```

3.  Validation Pipe: NestJS cung cấp sẵn một ValidationPipe từ thư viện class-validator và class-transformer để thực hiện kiểm tra tính hợp lệ và biến đổi dữ liệu.

```ts
import { ValidationPipe } from '@nestjs/common';

@Controller('cats')
@UsePipes(new ValidationPipe())
export class CatsController {
  @Post()
  create(@Body() createCatDto: CreateCatDto) {
    return `This action creates a new cat with name: ${createCatDto.name}`;
  }
}
```

=>Pipes là một tính năng mạnh mẽ trong NestJS cho phép bạn kiểm tra, biến đổi và xử lý dữ liệu đầu vào trước khi nó được chuyển tới các phương thức xử lý trong controller. Điều này giúp tăng tính đáng tin cậy và linh hoạt trong việc xử lý dữ liệu trong ứng dụng của bạn, đồng thời giúp mã nguồn trở nên dễ bảo trì và tái sử dụng hơn.

- transformation:định dạng type cho dữ liệu chuyền vào
- validation: kiểm tra dữ liệu dầu vào

## Interceptor

-> Trong NestJS, interceptor là một thành phần quan trọng để kiểm soát quá trình xử lý của request và response. Interceptor cho phép bạn thực hiện các logic xử lý trước khi request đến controller và sau khi response từ controller được trả về. Điều này giúp tách biệt logic xử lý chung và logic xử lý cụ thể của từng request.

#### Các Đặc Điểm Của Interceptor Trong NestJS

1. Xử Lý Trước Và Sau Controller: Interceptor được chia làm hai loại chính:

- Global Interceptor: Áp dụng cho tất cả các request đi qua ứng dụng.
- Local Interceptor: Áp dụng chỉ cho một controller hoặc một route cụ thể.

2.  Chức Năng Chính

- Trước khi Controller xử lý request: Interceptor có thể thực hiện các thay đổi hoặc kiểm tra dữ liệu request, log thông tin, xác thực người dùng, ...
- Sau khi Controller xử lý request và trước khi response được gửi đi: Interceptor có thể xử lý dữ liệu trả về từ controller, log kết quả, thay đổi response, ...

3. Được Sử Dụng Để:

- Authentication: Xác thực người dùng trước khi cho phép truy cập vào các route bảo vệ.
- Logging: Ghi lại thông tin quan trọng như thời gian xử lý request, thao tác database, ...
- Error Handling: Xử lý các ngoại lệ và lỗi từ các controller.
- Transform Data: Biến đổi dữ liệu vào ra từ controller cho phù hợp với định dạng yêu cầu hoặc phản hồi của client.
- Caching: Quản lý bộ nhớ đệm để tối ưu hóa hiệu suất ứng dụng.

## Middleware

-> trong dữ án middleware -> before interceptor -> controller -> after interceptor

## Guard(bảo vệ)

-> cho phép hoặc tự chôi truy cấp router
