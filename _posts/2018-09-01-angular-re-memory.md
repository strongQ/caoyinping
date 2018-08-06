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
    然后调用： data=await this.httpclient.post(domain+ AppGlobal.API.instructMs+`/task/back/approval/filter/page`,applySearch,this.httpOptions).toPromise();
