# Mango
Mango is a DSL which syntax is very similar to Objective-C，Mango is also an iOS  App hotfix SDK. You can use Mango method replace any Objective-C method.


## Example
```objc
#import "AppDelegate.h"
#import <MangoFix/MangoFix.h>

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    NSString *path = [[NSBundle mainBundle] pathForResource:@"demo" ofType:@"mg"];
    NSURL *scriptUrl = [NSURL fileURLWithPath:path];
    MFContext *context = [[MFContext alloc] init];
    [context evalMangoScriptWithURL:scriptUrl];
    return YES;
}

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self.view addSubview:[self genView]];
}

- (UIView *)genView{
    UIView *view = [[UIView alloc] initWithFrame:CGRectMake(50, 100, 150, 200)];
    return view;
}

@end

```

```objc
class ViewController:UIViewController{

- (UIView *)genView{
    UIView *view = UIView.alloc().initWithFrame:(CGRectMake(50, 100, 150, 200));
    view.backgroundColor = UIColor.redColor();
    return view;
}

}

```

## Installation
### CocoaPods
1. Add `pod 'MangoFix'` to your Podfile.
* Run `pod install` or `pod update`.
* Import `<MangoFix/MangoFix.h>`

## Usage
### Objective-C

1. `#import <MangoFix/MangoFix.h>`
2. `exec Mango Script by [context evalMangoScriptWithSourceString:@""];`

```objc
MFContext *context = [[MFContext alloc] init];
// exec mango file from network
[NSURLConnection sendAsynchronousRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://xxx/demo.mg"]] queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
    NSString *script = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
    [context evalMangoScriptWithSourceString:script];
}];
	
// exec local mango file
NSString *sourcePath = [[NSBundle mainBundle] pathForResource:@"demo" ofType:@"mg"];
NSString *script = [NSString stringWithContentsOfFile:sourcePath encoding:NSUTF8StringEncoding error:nil];
[context evalMangoScriptWithSourceString:script];
```

### Mango
#### Quick start

```objc
/**
demo.mg
*/

//声明一个自定义结构体
declare struct MyStruct {
    typeEncoding:"{MyStruct=dd}",
    keys:x,y
}

class ViewController:UIViewController {

- (void)sequentialStatementExample{
//变量定义
    NSString *text = @"1";

    self.resultView.text = text;

}

- (void)ifStatementExample{
	int a = 2;
	int b = 2;

	NSString *text;
	if(a > b){
		text = @"执行结果: a > b";
	}else if (a == b){
		text = @"执行结果: a == b";
	}else{
		text = @"执行结果: a < b";
	}
	self.resultView.text = text;
}

- (void)switchStatementExample{
	int a = 2;
	NSString *text;
	switch(a){
		case 1:{
			text = @"match 1";
			break;
		}
		case 2:{} //case 后面的一对花括号不可以省略
		case 3:{
			text = @"match 2 or 3";
			break;
		}
		case 4:{
			text = @"match 4";
			break;
		}
		default:{
			text = @"match default";
		}
	}
	self.resultView.text = text;
}

- (void)forStatementExample{
	NSString *text = @"";
	for(int i = 0; i < 20; i++){
		text = text + i + @", ";
		if(i == 10){
			break;
		}
	}
	self.resultView.text = text;
}

- (void)forEachStatementExample{
	NSArray *arr = @[@"a", @"b", @"c", @"d", @"e", @"f", @"g", @"g", @"i", @"j",@"k"];
	NSString *text = @"";
	for(id element in arr){
		text = text + element + @", ";
		if(element.isEqualToString:(@"i")){
			break;
		}

	}
	self.resultView.text = text;
}

- (void)whileStatementExample{
	int a;
	while(a < 10){
		if(a == 5){
			break;
		}
		a++;
	}
	self.resultView.text = @""+a;
}

- (void)doWhileStatementExample{
	int a = 0;
	do{
		a++;
	}while(NO);
	self.resultView.text = @""+a;

}

- (void)blockStatementExample{
	Block catStringBlock = ^NSString *(NSString *str1, NSString *str2){
								NSString *result = str1.stringByAppendingString:(str2);
								return result;
							};
	NSString *result = catStringBlock(@"hello ", @"world!");
	self.resultView.text = result;
}


- (void)paramPassingExampleWithBOOLArg:(BOOL)BOOLArg intArg:(int) intArg uintArg:(NSUInteger)uintArg blockArg:(Block)blockArg  objArg:(id)objArg {
	NSString *text = @"";
	text += @"BOOLArg:" + BOOLArg + @",\n";
	text += @"intArg:" + intArg + @",\n";
    text += @"uintArg:" + uintArg + @",\n";
	text += @"Block执行结果:" + blockArg(@"hello", @"mango") + @"\n";
	text += @"objArg:" + objArg;
	self.resultView.text = text;
}


- (struct MyStruct)paramPassingExampleWithStrut:(struct CGRect)rect{
    struct MyStruct myStruct = {x:(rect.origin.x + 100), y:(rect.origin.x + 10)};
    return myStruct;
}

- (Block)returnBlockExample{
	NSString *prefix = @"mango: ";
	Block catStringBlock = ^NSString *(NSString *str1, NSString *str2){
		NSString *result = str1.stringByAppendingString:(str2);
		return prefix + result;
	};
	return catStringBlock;
}


- (void)callOriginalImp{
    self.ORGcallOriginalImp();
}

- (void)createAndOpenNewViewControllerExample{
	SubMyController *vc = SubMyController.alloc().init();
	self.navigationController.pushViewController:animated:(vc,YES);

}

//类方法替换示例
+ (void)classMethodExapleWithInstance:(ViewController *)vc{
	vc.resultView.text = @"here is Mango  Class Method " + self;
}

//条件注释示例
#If($systemVersion.doubleValue() > 12.0 )
- (void)conditionsAnnotationExample{
self.resultView.text = @"here is Mango method";
}


//GCD示例
- (void)gcdExample{
	dispatch_queue_t queue = dispatch_queue_create("com.plliang19.mango", DISPATCH_QUEUE_CONCURRENT);
	dispatch_async(queue, ^{
		NSLog(@"mango dispatch_async");
	});
	dispatch_sync(queue, ^{
		NSLog(@"mango dispatch_sync");
	});
}


}


class SuperMyController:UIViewController{
- (void)viewDidLoad {
    super.viewDidLoad();
    self.view.backgroundColor = UIColor.blueColor();
}

}


class SubMyController:SuperMyController {
@property (strong, nonatomic) UIView *rotateView;
- (void)viewDidLoad {
        super.viewDidLoad();
		self.title = @"Magno 创建自定义ViewController";
		double width = 100;
		double height = 100;
		double x = self.view.frame.size.width/2 - width/2;
		double y = self.view.frame.size.height/2 - height/2;
		UIView *view = MyView.alloc().initWithFrame:(CGRectMake(x, y, width, height));
		self.view.addSubview:(view);
		view.backgroundColor = UIColor.redColor();
		self.rotateView = view;


}

}


```

#### Mango Type usage
 Mango support type as fllow: 
 
##### void
  	equivalent to Objective-C `void`.
	
#### BOOL
  	equivalent to Objective-C `BOOL`.
	
##### uint
	equivalent to Objective-C `unsigned char`、`unsigned short`、`unsigned int`、`unsigned long`、`unsigned long long`、`NSUInteger`. 
	  
#### int
  	equivalent to Objective-C `char`、`short`、`int`、`long`、`long long`、`NSInteger`. 
	
##### double
  	equivalent to Objective-C `double`、`float`、`CGFloat`. 
	
#### id
  	equivalent to Objective-C `id`.
	
#### OCClassName *
  	NSString *str = @"";
	
#### Block
  	Block blokc = ^id(id arg){};
	
#### Class
  	Class clazz = NSString.class();
	
#### struct
  	struct CGRect rect;// must add struct keyword  before structure variables defined.
	
#### Pointer
 	Pointer ptr; // C pointer. 
	
	
