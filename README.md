# JSONModel-Dictionary-To-Model
JSONModel的使用
查看原文：http://www.heyuan110.com/?p=1155

你在把字典转成object的时候还在按下面这样:

self.id = [jsonDict objectForKey:@"id"]; 
self.name = [jsonDict objectForKey:@"name"]; 
self.profileImageBig = [jsonDict objectForKey:@"profile_image_big"]; 
self.profileImageSmall = [jsonDict objectForKey:@"profile_image_small"]; 
self.profileImageSquare = [jsonDict objectForKey:@"profile_image_square"]; 
self.firstName = [jsonDict objectForKey:@"firstName"]; 
self.familyName = [jsonDict objectForKey:@"familyName"]; 
self.age = [jsonDict objectForKey:@"age"];

这样做你就out了，繁琐而且麻烦，得判断值的nil，null，类型等。使用JSONModel这样即可搞定

@interface MyModel: JSONModel @property (strong, nonatomic) NSString* id; 
@property (strong, nonatomic) NSString* name; (etc...) 
@end

添加JSONModel的方式

pod管理的直接 pod 'JSONModel'

其它的直接去下载包:https://github.com/icanzilb/JSONModel

教程参考:

http://www.touch-code-magazine.com/JSONModel/

http://www.raywenderlich.com/12139/introduction-to-cocoapods

简单介绍一下几个方面

    例如 @property (strong, nonatomic) NSString<index>* name;
    你看到有一个Index,这个表示指定一个索引，作用就是可以直接在数组中查找符合条件的对象,
    例如查找数组中object的name是sharofat的对象可以像下面这样写:

    //查找index为sharofat的 NSArray *loans = feed.loans;
    NSLog(@"modelWithIndexValue --->%@",[loans modelWithIndexValue:@"Sharofat"]);

    object的数组和dict的数组相互转换,objce转json，dict

    //将model的array转成dict的array 
      NSMutableArray *dictArray = [LoanModel arrayOfDictionariesFromModels:feed.loans];
      NSLog(@"arrayOfDictionariesFromModels===>%@",dictArray);
    //将dict的array转成model的array 
      NSMutableArray *modelArray = [LoanModel arrayOfModelsFromDictionaries:dictArray];
      NSLog(@"arrayOfModelsFromDictionaries===>%@",modelArray); LoanModel* loan = feed.loans[indexPath.row]; 
      NSLog(@"loan.toDictionary--->%@",loan.toDictionary); 
      NSLog(@"loan.toJSONString--->%@",loan.toJSONString);

    json，dict转object时判断value

    -(BOOL)validate:(NSError**)err { if ([self.name isEqual:@"Winfred"]) { self.name = @"Winfred rewrite name";
      // return NO; 
    } 
      NSLog(@"Loan of %@", self.name); 
      NSLog(@"sector of %@", self.modelSector);
      NSLog(@"plandate of %@", self.plandate); 
      return YES; 
    }

    很重要的keyMapper，指定映射值,如果不指定就是默认的

        转换带下划线的,例如:user_name 转换对应的key就是userName


        +(JSONKeyMapper*)keyMapper { return [JSONKeyMapper mapperFromUnderscoreCaseToCamelCase]; }

        自定义key,例如: planned_expiration_date转换想对应plandate

        +(JSONKeyMapper*)keyMapper { return [[JSONKeyMapper alloc] initWithJSONToModelBlock:^NSString *(NSString *keyName) {
        if ([keyName isEqual:@"planned_expiration_date"]) {
            return @"plandate"; 
        }
        else if ([keyName isEqual:@"sector"]) 
        { 
            return @"modelSector"; 
        } else {
            return keyName; 
        } 
        } 
        modelToJSONBlock:^NSString *(NSString *keyName) { 
        if ([keyName isEqual:@"plandate"]) {
            return @"planned_expiration_date"; 
          }else if ([keyName isEqual:@"modelSector"]){ 
            return @"sector"; 
          }else { 
            return keyName; 
          } 
        }];
    }

        也可以像下面这样写:
        +(JSONKeyMapper*)keyMapper { 
          return [[JSONKeyMapper alloc]initWithDictionary:@{@"sector":@"modelSector"}]; 
        } 
        
  

指定定义的key的类型

    <optional>表示字段可选,例如

    //链接字段是可选的，转换的时候允许link未空 @property (nonatomic,strong) NSString</optional><optional> *link;

    <index>表示索引，参照1

    <convertondemand>转换对象数组,例如:

    //表示数组是LoanModel对象 @property (strong, nonatomic) NSArray<loanmodel , ConvertOnDemand>* loans;

