import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Router } from '@angular/router';
import { Observable } from 'rxjs';
import { Company } from '../model/company';
import { AuthService } from '../service/auth.service';
import { EstockService } from '../service/EStock.service';

@Component({
  selector: 'app-company',
  templateUrl: './company.component.html',
  styleUrls: ['./company.component.css']
})
export class CompanyComponent {
  
  companyForm!: FormGroup;
  companies: Company[] = [];
  searchText: string = '';
  constructor(private authService: AuthService, private http: HttpClient, private router: Router,private formBuilder: FormBuilder) { }
 
  ngOnInit() {
    this.companyForm = this.formBuilder.group({
      companyCode: ['', Validators.required],
      companyName: ['', Validators.required],
      companyCEO: ['', Validators.required],
      companyTurnover: ['', Validators.required],
      companyWebsite: ['', Validators.required],
      stockExchange: ['', Validators.required],
      //stockPrice: ['', Validators.required],
      //latestStockPrice: ['', Validators.required],
    });
    this.getAllCompanies();
    //this.getMov();
   
   
  }
  submitForm() {
    if (this.companyForm.invalid) {
      return;
    
    }
    console.log("inside")
    const url = 'http://localhost:8081/v1.0/market/company/register';
    const token = sessionStorage.getItem('token');
    const body = {
      companyCode: this.companyForm.value.comapnyCode,
      companyName: this.companyForm.value.companyName,
      companyCEO: this.companyForm.value.companyCEO,
      companyTurnover: this.companyForm.value.companyTurnover,
      companyWebsite: this.companyForm.value.companyWebsite,
      stockExchange: this.companyForm.value.stockExchange,
      //stockPrice: this.companyForm.value.stockPrice,
     // latestStockPrice: this.companyForm.value.latestStockPrice,
      
    };
    console.log(this.companyForm) 
    const httpOptions = {
      headers: new HttpHeaders({
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + token
      })
    };
 
    console.log('Company Name:', this.companyForm.value.companyName);
    
    this.http.post(url, body, httpOptions).subscribe(
      () => {
        console.log('Company added successfully');
        // Handle success response here
        this.getAllCompanies();
      },
      (error) => {
        console.error(error);
        // Handle error response here
      }
    );
  }
getAllCompanies() {
  const url = 'http://localhost:8081/v1.0/market/company/getAll';
  const token = sessionStorage.getItem('token');

  console.log("Token -> ", token);
 
  const httpOptions = {
    headers: new HttpHeaders({
      'Content-Type':  'application/json',
      'Authorization': 'Bearer ' + token
    })
  };
  console.log("httpOptions -> ", httpOptions);
  this.http.get<Company[]>(url, httpOptions).subscribe(movies => {
    this.companies = movies.map(movie => ({
      ...movie,
      editMode: false
    }));
    const latestStockPrices = this.companies.map(company => company.latestStockPrice);
        console.log('Latest Stock Prices:', latestStockPrices);
  });
}
}












<!DOCTYPE html>
<html lang="en">
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"
            integrity="sha384-C6RzsynM9kWDrMNeT87bh95OGNyZPhcTNXj1NW7RuBCsyN/o0jlpcV8Qyq46cDfL" crossorigin="anonymous"></script>

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>
    
    <style>
        
    </style>
</head>
<body>

<div class="container">
    <div class="card">
        <h1>Add Company Details</h1>
                
                <form (ngSubmit)="submitForm()">
                    <div class="form-group">
                        <input name="companyCode" class="form-control" type="number" placeholder="Enter Company CODE" required>
                    </div>
                    <div class="form-group">
                        <input name="companyName" class="form-control" type="text" placeholder="Enter Company Name" required>
                    </div>
                    <div class="form-group">
                        <input name="companyCEO" class="form-control" type="text" placeholder="Enter companyCEO" required>
                    </div>
                    <div class="form-group">
                        <input name="companyTurnover" class="form-control" type="number" placeholder="Enter companyTurnover" required>
                    </div>
                    <div class="form-group">
                        <input name="companyWebsite" class="form-control" type="text" placeholder="Enter companyWebsite" required>
                    </div>
                    <div class="form-group">
                        <input name="stockExchange" class="form-control" type="text" placeholder="Enter stockExchange" required>
                    </div>
                    <!-- <div class="form-group">
                        <input name="stockPrice" class="form-control" type="text" placeholder="Enter stockPrice" required>
                    </div> -->
                    <button class="btn btn-success" type="submit" >Add Details</button>
                </form><!-- Add other form fields as needed -->
       
    </div>

    <div class="card">
        <h1>View All Companies</h1>
        <button class="btn btn-info loginbtn" (click)="getAllCompanies()">Get All Company</button>
        <table class="table table-striped">
            <table class="table table-striped">
                <thead>
                <tr>
                    <th>COMPANY CODE</th>
                    <th>COMPANY NAME</th>
                    <th>CEO</th>
                    <th>TURNOVER</th>
                    <th>WEBSITE</th>
                    <th>STOCK EXCHANGE</th>
                    <th>STOCK PRICE</th>
                    <th>ACTION</th>
                </tr>
                </thead>
                <tbody>
                <tr *ngFor="let c of companies">
                    <td>{{c.companyCode}}</td>
                    <td>{{ c.companyName }}</td>
                    <td>{{ c.companyCEO }}</td>
                    <td>{{ c.companyTurnover}}</td>
                    <td>{{ c.companyWebsite }}</td>
                    <td>{{ c.stockExchange }}</td>
                    <td>{{ c.latestStockPrice }}</td>
            
                    <td>
                        <button class="btn btn-warning" >Update</button>
                        <button class="btn btn-danger" >Delete</button>
                    </td>
                </tr>
                </tbody>
            </table>
        </table>
    </div>

    <div class="card">
        <h1>Search Company</h1>
        <div id="searchBox">
            <input type="number">
            <button class="btn btn-warning loginbtn">Search</button>
            <table class="table table-striped">
                <table class="table table-striped">
                    <thead>
                    <tr>
                        <th>COMPANY CODE</th>
                        <th>COMPANY NAME</th>
                        <th>CEO</th>
                        <th>COMPANY TURNOVER</th>
                        <th>COMPANY WEBSITE</th>
                        <th>STOCK EXCHANGE</th>
                        <th>STOCK PRICE</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr >
                        <td>Anand</td>
                        <!-- <td>{{ r.companyName }}</td>
                        <td>{{ r.companyCEO }}</td>
                        <td>{{ r.turnover}}</td>
                        <td>{{ r.website }}</td>
                        <td>{{ r.stockExchange }}</td>
                        <td>{{ r.stockPrice }}</td> -->
                    </tr>
                    </tbody>
                </table>
            </table>
        </div>
    </div>
</div>

</body>
</html>
