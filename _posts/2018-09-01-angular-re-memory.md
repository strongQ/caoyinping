---

layout:        post
title:         angular4
description:   整理部分angular中的知识
keywords:      angular
categories:    [bs,angular]
tags:          [bs,angular]

---

----------------------------

### 1、使用Output装饰器
    Output 装饰器的作用是用来实现子组件将信息，通过事件的形式通知到父级组件。
    let numberEmitter: EventEmitter<number> = new EventEmitter<number>();
    numberEmitter.subscribe((value: number) => console.log(value));
    numberEmitter.emit(10);

  1、 使用 Output 装饰器更新 SimpleFormComponent 组件

     @Component({
     selector: 'app-simple-form',
     template: `
    <div>
     {{message}}
     <input #myInput type="text" [(ngModel)]="message">
     <button (click)="update.emit({text: message})">更新</button>
    </div>
     ,
     styles: []
    })
    export class SimpleFormComponent implements OnInit {
    @Input() message: string;
    @Output() update = new EventEmitter<{text: string}>();

    ngOnInit() { }
   }

   2、更新 AppComponent 组件

     <app-simple-form * ngFor="let message of mailService.messages;
     [message]="message.text"
     (update)="onUpdate(message.id, $event.text)">
     </app-simple-form>


### 2、异步post、get方法
    首先导入import { HttpClient ,HttpHeaders } from '@angular/common/http';
    httpOptions = {
        headers: new HttpHeaders({
          'Content-Type':  'application/json'

        })
      };
    然后调用： data=await this.httpclient.post(domain+ AppGlobal.API.instructMs+`/task/back/approval/filter/page`,applySearch,this.httpOptions).toPromise();

### 3、与cef客户端交互

    1、首先在构造函数中定义全局对象
    window['appComponentRef']={component:this,zone:_ngZone };
    2、然后在初始化方法中注册外部方法
    window['angularApply'].registerCallback(    
       this.GetData
    )
    GetData(type:string,sn:string,ctrl:string,status:string){
    let applyType:ApplyType=+type;
    window['appComponentRef'].zone.run(()=>{

      // inform为component中的方法
      window['appComponentRef'].component.inform(sn,applyType,ctrl,status);

    }
    )   
  }
M

 ### 4、访问dom元素

     @ViewChild('myInput') input: ElementRef;

     constructor(private renderer: Renderer) {}

     ngAfterViewInit() {
     this.renderer.invokeElementMethod(
     this.input.nativeElement, 'focus');
     this.renderer.setElementStyle(this.input.nativeElement, 'backgroundColor', 'red');
    }

### 5、懒加载模块
1、定义公共模块，导出第三方组件等供懒加载模块使用

    @NgModule({
    declarations: [AlramViewComponent,GenderPipe,AlarmreportComponent,YesNoPipe,lostPipe
     ],
    imports: [
    CommonModule,
    NgZorroAntdModule,
    RouterModule,
    ReactiveFormsModule,
    FormsModule,
    ChartsModule,
    NgxPermissionsModule
    ],
    exports:[
    NgxPermissionsModule,
    CommonModule,
    NgZorroAntdModule,
    AlramViewComponent,
    AlarmreportComponent,
    GenderPipe,
    YesNoPipe,
    lostPipe,
    RouterModule,
    ReactiveFormsModule,
    FormsModule,
    ChartsModule
    ],

    })

  2、懒加载模块必须添加module文件

    @NgModule({
    declarations: [AnaysistabsComponent],
    imports: [
    CommonModule,
    NzCommonModule,
    AnaysistabsRoutingModule
    ]
    })
    export class AnaysistabsModule { }

  3、改变路由模式

    const routes: Routes = [
      {
        path:'',
        component:ApplytabsComponent,
        children:[
            {path:'',redirectTo:'delay/1',pathMatch:'full'},
            {path:'rollback/:status',loadChildren:'../rollbacktab/rollbacktab.module#RollbackTabModule'},
            {path:'rollback/:status/:sn',loadChildren:'../rollbacktab/rollbacktab.module#RollbackTabModule'},
            {path:'updown/:status',loadChildren:'../up-downtab/up-downtab.module#UpDownTabModule'},
            {path:'updown/:status/:sn/:ctrl',loadChildren:'../up-downtab/up-downtab.module#UpDownTabModule'},
            {path:'trans/:status',loadChildren:'../transtab/transtab.module#TransTabModule'},
            {path:'trans/:status/:sn/:ctrl',loadChildren:'../transtab/transtab.module#TransTabModule'},
            {path:'delay/:status/:sn',loadChildren:'../delaytab/delaytab.module#DelayTabModule'},
            {path:'delay/:status',loadChildren:'../delaytab/delaytab.module#DelayTabModule'}
        ]
       ];
     }

     imports: [RouterModule.forChild(routes)],
