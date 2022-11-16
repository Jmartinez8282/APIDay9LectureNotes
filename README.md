# APIDay9LectureNotes

# DAY9

# DATA TRANSFER OBJECTS (DTO’S)

For all he data transfer objects regarding the RPG characters

When you have smaller objects that do not consist of every property of the corresponding model.  When we create a database table for our RPG characters later in this course, we could add properties like the created and modified date .

We don’t want to send this data to the client, so we map certain properties of the model to the DTO

1. Lets Create a Folder in the root called Dtos
    1. In that folder create another folder and name that one : Character
        1. Inside the Character Folder
            1. Create a new file class name it GetCharacterDto
                1. Now in the GetCharacterDto.cs

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace dotnet_rpg.Dtos.Character
{
    public class GetCharacterDto
    {
        
    }
}
```

1. Add another Class name it : AddCharacterDto
    1. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace dotnet_rpg.Dtos.Character
    {
        public class AddCharacterDto
        {
            
        }
    }
    ```
    
2. Go to the Models folder under Character.cs
    1. Copy and paste the following
    2. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace dotnet_rpg.Models
    {
        public class Character
        {
            public int Id { get; set; }
    
            public string Name { get; set; } = "Frodo";
    
            public int HitPoints { get; set; } = 100;
    
            public int Strength { get; set; } = 10;
    
            public int Defense { get; set; } = 10;
    
            public int Intelligence { get; set; } = 10;
    
            public RpgClass Class { get; set; } = RpgClass.Knight;
        }
    }
    ```
    
    1. Go to the GetCharacterDto.cs and pate it
        1. 
        
        ```csharp
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Threading.Tasks;
        
        namespace dotnet_rpg.Dtos.Character
        {
            public class GetCharacterDto
            {
                   public int Id { get; set; }
        
                public string Name { get; set; } = "Frodo";
        
                public int HitPoints { get; set; } = 100;
        
                public int Strength { get; set; } = 10;
        
                public int Defense { get; set; } = 10;
        
                public int Intelligence { get; set; } = 10;
        
                public RpgClass Class { get; set; } = RpgClass.Knight;
            }
        }
        ```
        
        1. Do the same with the AddCharacterDto.cs but remove the Id get set.
            1. 
            
            ```csharp
            using System;
            using System.Collections.Generic;
            using System.Linq;
            using System.Threading.Tasks;
            
            namespace dotnet_rpg.Dtos.Character
            {
                public class AddCharacterDto
                {
                  
            
                    public string Name { get; set; } = "Frodo";
            
                    public int HitPoints { get; set; } = 100;
            
                    public int Strength { get; set; } = 10;
            
                    public int Defense { get; set; } = 10;
            
                    public int Intelligence { get; set; } = 10;
            
                    public RpgClass Class { get; set; } = RpgClass.Knight;
                }
            }
            ```
            
        2. Go to the ICharacterService.cs and add replace the Character with GetCharacterDto
            1. 
            
            ```csharp
            using System;
            using System.Collections.Generic;
            using System.Linq;
            using System.Threading.Tasks;
            
            namespace dotnet_rpg.Services.CharacterService
            {
                public interface ICharacterService
                {
                    Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters();
            
                    Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id);
            
                    Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter);
                }
            }
            ```
            
        3. Same with CharacterService.cs
            1. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace dotnet_rpg.Services.CharacterService
{
    public class CharacterService : ICharacterService
    {

        private static List<Character> characters = new List<Character>{
            new Character(),
            new Character { Id = 1,Name = "sam"}
        };
        public async Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter)
        {
           var serviceResponse = new ServiceResponse<List<GetCharacterDto>>();
        
           characters.Add(newCharacter);
           serviceResponse.Data = characters; 
           return serviceResponse;
        }

        public async Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters()
        {
          return new ServiceResponse<List<GetCharacterDto>> { Data = characters};
        }

        public async Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id)
        {
            var serviceResponse = new ServiceResponse<GetCharacterDto>();
            var character = characters.FirstOrDefault(c => c.Id == id);
            serviceResponse.Data = character;
            return serviceResponse;
        }
    }
}
```

1. Go to the CharacterController.cs and add the CharacterDto
    1. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using dotnet_rpg.Services.CharacterService;
    using Microsoft.AspNetCore.Mvc;
    
    namespace dotnet_rpg.Controllers
    {
    
        [ApiController]
        [Route("api/[controller]")]
        public class CharacterController : ControllerBase
        {
            private readonly ICharacterService characterService;
            
            public CharacterController(ICharacterService characterService)
            {
                this.characterService = characterService;
                
            }
            
            [HttpGet("GetAll")]
            public async Task<ActionResult<ServiceResponse<List<GetCharacterDto>>> Get()
            {
                return Ok(await this.characterService.GetAllCharacters());
            }
    
            [HttpGet("{id}")]
            public async Task<ActionResult<ServiceResponse<GetCharacterDto>>> GetSingle(int id)
            {
                return Ok(await this.characterService.GetCharacterById(id));
            }
    
             [HttpPost]   
            public async Task<ActionResult<ServiceResponse<List<GetCharacterDto>>>> AddCharacter(AddCharacterDto newCharacter)
            {
                
                return Ok(await this.characterService.AddCharacter(newCharacter));
            }
        }
    }
    ```
    

# AUTOMAPPER

1. Lets install auto mapper
    1. [https://www.nuget.org/packages/AutoMapper.Extensions.Microsoft.DependencyInjection](https://www.nuget.org/packages/AutoMapper.Extensions.Microsoft.DependencyInjection)
        1. run this command in the CLI: dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection 
    2. Now we need to register automapper in our Program.cs file
        1. 
        
        ```csharp
        global using dotnet_rpg.Models;
        using dotnet_rpg.Services.CharacterService;
        
        var builder = WebApplication.CreateBuilder(args);
        
        // Add services to the container.
        
        builder.Services.AddControllers();
        // Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
        builder.Services.AddEndpointsApiExplorer();
        builder.Services.AddSwaggerGen();
        builder.Services.AddAutoMapper(typeof(Program).Assembly);
        builder.Services.AddScoped<ICharacterService, CharacterService>();
        
        var app = builder.Build();
        
        // Configure the HTTP request pipeline.
        if (app.Environment.IsDevelopment())
        {
            app.UseSwagger();
            app.UseSwaggerUI();
        }
        
        app.UseHttpsRedirection();
        
        app.UseAuthorization();
        
        app.MapControllers();
        
        app.Run();
        ```
        
    
    2. Now we need an instance of the mapper in our Service CharacterService.cs
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using AutoMapper;
    using dotnet_rpg.Dtos.Character;
    
    namespace dotnet_rpg.Services.CharacterService
    {
        public class CharacterService : ICharacterService
        {
    
            private static List<Character> characters = new List<Character>{
                new Character(),
                new Character { Id = 1,Name = "sam"}
            };
    
    //ctor short cut to make the constructor
            private readonly IMapper mapper;
    				//hover over IMapper and add using AutoMapper, Hover over mapper and create and assign field mapper
            public CharacterService(IMapper mapper)
            {
                this.mapper = mapper;
            }
            public async Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter)
            {
               var serviceResponse = new ServiceResponse<List<GetCharacterDto>>();
            
               characters.Add(newCharacter);
               serviceResponse.Data = characters; 
               return serviceResponse;
            }
    
            public async Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters()
            {
              return new ServiceResponse<List<GetCharacterDto>> { Data = characters};
            }
    
            public async Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id)
            {
                var serviceResponse = new ServiceResponse<GetCharacterDto>();
                var character = characters.FirstOrDefault(c => c.Id == id);
                serviceResponse.Data = this.mapper.Map<GetCharacterDto>(character);
                return serviceResponse;
            }
        }
    }
    ```
    
1. Now we need to modify our service response

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using AutoMapper;
using dotnet_rpg.Dtos.Character;

namespace dotnet_rpg.Services.CharacterService
{
    public class CharacterService : ICharacterService
    {

        private static List<Character> characters = new List<Character>{
            new Character(),
            new Character { Id = 1,Name = "sam"}
        };
        
        private readonly IMapper mapper;

        public CharacterService(IMapper mapper)
        {
            this.mapper = mapper;
        }
        public async Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter)
        {
           var serviceResponse = new ServiceResponse<List<GetCharacterDto>>();
										        //2
           characters.Add(this.mapper.Map<Character>(newCharacter));
           serviceResponse.Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList(); 
           return serviceResponse;
        }

        public async Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters()
        {
														//3
          return new ServiceResponse<List<GetCharacterDto>> { Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList()};
        }

        public async Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id)
        {
            var serviceResponse = new ServiceResponse<GetCharacterDto>();
            var character = characters.FirstOrDefault(c => c.Id == id);
																	//1
            serviceResponse.Data = this.mapper.Map<GetCharacterDto>(character);
            return serviceResponse;
        }
    }
}
```

1. Now lets test it with Swagge
    1. dotnet watch run
        1. We get a Missing type map configuration or unsupported mapping error
            1. Lets fix it
2. In root level create new C# class and name it AutoMapperProfile.cs
    1. 
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using AutoMapper;
    using dotnet_rpg.Dtos.Character;
    
    namespace dotnet_rpg
    {                         //hover over Profile and add using AutoMapper
        public class AutoMapperProfile : Profile
        {
    					//shortcut ctor
            public AutoMapperProfile()
            {
    						// hover over GetCharacterDto using (name of your project) Dtos.Character
                CreateMap<Character,GetCharacterDto>();
            }
        }
    }
    ```
    
3. Lets go quit our terminal and relaunch it
    1. dotnet watch run
        1. Our GetAll works
            1. our Get single Character works
                1. But our Post does not workWe get an Missing map configuration or unsupported mapping
                2. Lets fix it
4. We have to create another map
    1. Lets go to our AutoMapperProfile and add the following code

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using AutoMapper;
using dotnet_rpg.Dtos.Character;

namespace dotnet_rpg
{
    public class AutoMapperProfile : Profile
    {
        public AutoMapperProfile()
        {
            CreateMap<Character,GetCharacterDto>();
            CreateMap<AddCharacterDto,Character>();
        }
    }
}
```

1. Let test it again in Swagger
    1. dotnet watch run
        1. Now our Post works
            1. But our ID = 0
                1. Lets fix this by generating a proper ID ourselfs
2. Lets go to our CharacterService.cs
    1. Lets Modify our AddCharacter Method
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using AutoMapper;
    using dotnet_rpg.Dtos.Character;
    
    namespace dotnet_rpg.Services.CharacterService
    {
        public class CharacterService : ICharacterService
        {
    
            private static List<Character> characters = new List<Character>{
                new Character(),
                new Character { Id = 1,Name = "sam"}
            };
            
            private readonly IMapper mapper;
    
            public CharacterService(IMapper mapper)
            {
                this.mapper = mapper;
            }
            public async Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter)
            {
               var serviceResponse = new ServiceResponse<List<GetCharacterDto>>();
               Character character = this.mapper.Map<Character>(newCharacter);
    
               character.Id = characters.Max(c => c.Id) + 1; 
               characters.Add(character);       
               serviceResponse.Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList(); 
               return serviceResponse;
            }
    
            public async Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters()
            {
              return new ServiceResponse<List<GetCharacterDto>> { Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList()};
            }
    
            public async Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id)
            {
                var serviceResponse = new ServiceResponse<GetCharacterDto>();
                var character = characters.FirstOrDefault(c => c.Id == id);
                serviceResponse.Data = this.mapper.Map<GetCharacterDto>(character);
                return serviceResponse;
            }
        }
    }
    ```
    

1. Test in with Swagger
    1. dotnet watch run

# MODIFY A CHARACTER WITH PUT

1. To Modify or update an RPG character, we have to add a new method to the ICharacterService.cs

1. The CharacterService.cs 
2. The CharacterController

1. Lets go to our ICharacterService.cs first

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using dotnet_rpg.Dtos.Character;

namespace dotnet_rpg.Services.CharacterService
{
    public interface ICharacterService
    {
        Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters();

        Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id);

        Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter);

          Task<ServiceResponse<GetCharacterDto>> UpdateCharacter(UpdateCharacterDto updateCharacter);

    }
}
```

1. Next got ot Dtos\Character folder and createa an new class name it UpdateCharacterDto

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace dotnet_rpg.Dtos.Character
{
    public class UpdateCharacterDto
    {
   
    }
}
```

1. Next copy the copy and paste the properites from the GetCharacterDto.cs to the UpdateCharacterDto.cs

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace dotnet_rpg.Dtos.Character
{
    public class UpdateCharacterDto
    {
        public int Id { get; set; }

        public string Name { get; set; } = "Frodo";

        public int HitPoints { get; set; } = 100;

        public int Strength { get; set; } = 10;

        public int Defense { get; set; } = 10;

        public int Intelligence { get; set; } = 10;

        public RpgClass Class { get; set; } = RpgClass.Knight;
    }
}
```

1. Lets Go to our CharacterService.cs
    1. Lets implement our Interface automaticly by hovering over ICharacterService and selecting implement interface
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using AutoMapper;
    using dotnet_rpg.Dtos.Character;
    
    namespace dotnet_rpg.Services.CharacterService
    {
        public class CharacterService : ICharacterService
        {
    
            private static List<Character> characters = new List<Character>{
                new Character(),
                new Character { Id = 1,Name = "sam"}
            };
            
            private readonly IMapper mapper;
    
            public CharacterService(IMapper mapper)
            {
                this.mapper = mapper;
            }
            public async Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter)
            {
               var serviceResponse = new ServiceResponse<List<GetCharacterDto>>();
               Character character = this.mapper.Map<Character>(newCharacter);
    
               character.Id = characters.Max(c => c.Id) + 1; 
               characters.Add(character);       
               serviceResponse.Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList(); 
               return serviceResponse;
            }
    
            public async Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters()
            {
              return new ServiceResponse<List<GetCharacterDto>> { Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList()};
            }
    
            public async Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id)
            {
                var serviceResponse = new ServiceResponse<GetCharacterDto>();
                var character = characters.FirstOrDefault(c => c.Id == id);
                serviceResponse.Data = this.mapper.Map<GetCharacterDto>(character);
                return serviceResponse;
            }
    
            public Task<ServiceResponse<GetCharacterDto>> UpdateCharacter(UpdateCharacterDto updateCharacter)
            {
                throw new NotImplementedException();
            }
        }
    }
    ```
    
    1. Inside our UpdateCharacter method in our CharacterService.cs

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using AutoMapper;
using dotnet_rpg.Dtos.Character;

namespace dotnet_rpg.Services.CharacterService
{
    public class CharacterService : ICharacterService
    {

        private static List<Character> characters = new List<Character>{
            new Character(),
            new Character { Id = 1,Name = "sam"}
        };
        
        private readonly IMapper mapper;

        public CharacterService(IMapper mapper)
        {
            this.mapper = mapper;
        }
        public async Task<ServiceResponse<List<GetCharacterDto>>> AddCharacter(AddCharacterDto newCharacter)
        {
           var serviceResponse = new ServiceResponse<List<GetCharacterDto>>();
           Character character = this.mapper.Map<Character>(newCharacter);

           character.Id = characters.Max(c => c.Id) + 1; 
           characters.Add(character);       
           serviceResponse.Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList(); 
           return serviceResponse;
        }

        public async Task<ServiceResponse<List<GetCharacterDto>>> GetAllCharacters()
        {
          return new ServiceResponse<List<GetCharacterDto>> { Data = characters.Select(c => this.mapper.Map<GetCharacterDto>(c)).ToList()};
        }

        public async Task<ServiceResponse<GetCharacterDto>> GetCharacterById(int id)
        {
            var serviceResponse = new ServiceResponse<GetCharacterDto>();
            var character = characters.FirstOrDefault(c => c.Id == id);
            serviceResponse.Data = this.mapper.Map<GetCharacterDto>(character);
            return serviceResponse;
        }

        public Task<ServiceResponse<GetCharacterDto>> UpdateCharacter(UpdateCharacterDto updateCharacter)
        {
            ServiceResponse<GetCharacterDto> response = new ServiceResponse<GetCharacterDto>();
            Character character = characters.FirstOrDefault(c => c.Id == updateCharacter.Id);

            character.Name = updateCharacter.Name;
            character.HitPoints = updateCharacter.HitPoints;
            character.Strength = updateCharacter.Strength;
            character.Defense = updateCharacter.Defense;
            character.Intelligence = updateCharacter.Intelligence;
            character.Class = updateCharacter.Class;

            response.Data = this.mapper.Map<GetCharacterDto>(character);
						
						return response;
        }
    }
}
```
