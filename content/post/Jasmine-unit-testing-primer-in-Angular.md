title: Karma + Jasmine unit testing components in Angular 2+\
classes: language-javascript\
startDate:2018-04-25T20:11:03+02:00\
pubDate:2019-01-14T23:10:40+02:00

Karma / Jasmine (unit) Tests for components in an Angular project
=================================================================

Part 2 of the Testing Angular series: The basics of testing components.
-----------------------------------------------------------------------

Testing services is fairly straightforward, you can treat them as regular classes with methods in there. For testing Components requires us to make draw a line, do we want to check just functions in the component? or do we want to check the component working to do what it is intended do do? this is where some people make the difference between unit and integration tests. Here we will bundle them together as unit, a unit of functionality called component. There will be plenty of space for e2e tests in a later post.

Make the component render (not crash)
-------------------------------------

Ugly as that may sound, that is your first step. All component tests start with just trying to instantiate the component in a dummy app, this is all handled by TestBed, an Angular specific testing utility library.

Because the component is isolated, all it usually is imported and declared in the Module, **during testing, these dependencies are not automatically available to the component**, so you have to declare them manually. Let's look at this sample component.

bc.. // sample.component.ts\
import { Component, OnInit } from '`angular/core';
import { ThingsService } from '../service/things.service';
import { ActivatedRoute, Router } from '`angular/router';\
import { Observable } from 'rxjs';

@Component({\
selector: 'app-sample',\
templateUrl: './sample.component.html',\
})\
export class SampleComponent implements OnInit {\
constructor(private thingsService: ThingsService, private router: Router, private route: ActivatedRoute) {}

listOfThings\$: Observable<any[]>;

ngOnInit(): void {\
this.route.url.subscribe(segments =&gt; {\
this.listOfThings\$ = this.thingsService.getThings(segments);\
});\
}

actionButtonClicked(): void {\
this.thingsService.doAction();\
}\
}

bc(language-makrup). // sample.component.html\
<mat-toolbar color="primary">\
<mat-toolbar-row>\
<img width="120" alt="Logo" src="/images/some-logo-image.png">

<button (click)="actionButtonClicked()">
Action!

</button>
</mat-toolbar-row>\
</mat-toolbar>\
<app-things-list></app-things-list>

And the imports in the spec file look like this:

    // sample.component.spec.ts
    beforeEach(async(() => {
        TestBed.configureTestingModule({
            declarations: [
                SampleComponent, // this is the component itself
                ThingsListComponent, // this is the <app-things-list> component instantiated in the template
            ],
            providers: [
                ThingsService // the service passed in the constructor
            ],
            imports: [
                MatToolbarModule, // this is one of the custom mat-* elements seen in the template
                RouterTestingModule, // this loads the Router and the ActivatedRoute "services"
                HttpClientModule, // this is actually a dependency of the ThingsService (this will bite you often)
            ],
        }).compileComponents();
    }));

For the dependencies you need to pay attention in 3 places:

1\. The Component Class `sample.component.ts`\
2. The Component Template `sample.component.html`\
3. The Module where the component is declared (maybe)

### Component Class

The first part where to look for dependencies is the **constructor**, here services get injected. Most of the services you create need to be passed in the providers. But they have their own dependencies. So, as a general recommendation, unless the service is trivial, **mock all services that a component needs**. But for now let's also look into the service dependencies and add them to the `providers` or `imports` arrays.

### Component Template

By looking at the template you have to quickly figure out if your component is instantiating **child components**, or directives within. In out case we have `<app-things-list></app-things-list>` which can either come from an external module or from this same module, if it's in this same module it has to be declared in the `declarations` array.

If it is an external module (from this same app or from a package) like `<mat-toolbar>` and `<mat-toolbar-row>`, we need to import it in the `imports` array.

### Component's Module

If you make small modules per each piece of functionality, going to the module where the component is declared and check the imports, you can probably figure out any missing import.

As a quick tip, some modules and services have a testing version like the `RouterModule` has `RouterTestingModule`, which actually prevents the need to mock the router.

Initial state for @Input()
--------------------------

As a warning, some components have @Input() vars, and the business logic expect them to be populated, but when testing, no component is passing in any values, breaking the test (sometimes with unclear messages). If you have any input you have three options:

1\. Have the component be instantiated by a testing wrapper component (the nice solution) which can pass different inputs to test different use cases.\
2. Pass the value of the `@input` to the instantiated component before any logic happens, removing anything related to the input from the constructor.\
3. Make some default value for all the inputs in your component.

Option 2 and 3 are not very recommended, because you should not code your application to fix your tests, only to be testable.

Outro
-----

There is a lot more to cover about testing components in Angular. This is to get familiar with loading the component dependencies.
