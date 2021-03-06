#.NET SDK - PromisePay API

[![Join the chat at https://gitter.im/PromisePay/promisepay-dotnet](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/PromisePay/promisepay-dotnet?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![NuGet version](https://badge.fury.io/nu/PromisePay%2Fpromisepay-dotnet.svg)](http://badge.fury.io/nu/PromisePay%2Fpromisepay-dotnet) [![Build Status](https://travis-ci.org/PromisePay/promisepay-dotnet.svg)](https://travis-ci.org/PromisePay/promisepay-dotnet) [![Code Climate](https://codeclimate.com/github/PromisePay/promisepay-dotnet/badges/gpa.svg)](https://codeclimate.com/github/PromisePay/promisepay-dotnet) 

#1. Installation
**NuGet:** Install PromisePay via NuGet package manager. The package name is '[PromisePay](https://www.nuget.org/packages/PromisePay.API.NET/0.0.1)'.

**Source:** Download latest sources from GitHub, add project into your solution and build it.


#2. Configuration

Before interacting with PromisePay API, you need to generate an API token. See [http://docs.promisepay.com/v2.2/docs/request_token](http://docs.promisepay.com/v2.2/docs/request_token) for more information.

Once you have recorded your API token, configure the .NET package - see below.

Add the below configuration to either the **App.config** or **Web.config** file, depending if it is a Windows, or Web application.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="PromisePay">
      <section name="Settings" type="PromisePayDotNet.Settings.SettingsHandler,PromisePayDotNet" />
    </sectionGroup>
  </configSections>
  <PromisePay>
    <Settings>
      <add key="ApiUrl" value="https://test.api.promisepay.com" />
      <add key="Login" value="YOUR LOGIN" />
      <add key="Password" value="YOUR PASSWORD" />
      <add key="Key" value="YOUR API KEY" />
    </Settings>
  </PromisePay>
</configuration>
```
**Environments**

	Prelive: https://test.api.promisepay.com
	Production: https://secure.api.promisepay.com

**Final configuration**

PromisePay API package is build using Dependency Injection principle. It makes integration into your application easy and seamless.

You will need to setup your DI container to bind interfaces and implementations of the package together.

If you use **Unity** container, just invoke init method, as it's shown below:

```cs
	var container = new UnityContainer();
	PromisePayDotNet.DI.InitUnityContainer.Init(container);
```

If you use another container, just bind interfaces from PromisePayDotNet.Interfaces to PromisePayDotNet.Implementations. You may use any lifecycle; implementations are stateless.


Then, you can use repositories from package, by resolving interface with container, or passing dependencies into constructor.

For details and example, please consider the following MSDN article:
[https://msdn.microsoft.com/ru-ru/library/dn178463(v=pandp.30).aspx](http://)

#3. Examples
##Tokens
##### Example 1 - Request session token
The below example shows the request for a marketplace configured to have the Item and User IDs generated automatically for them.

```cs
var repo = container.Resolve<ITokenRepository>();
var session_token = new Token {
	current_user = "seller",
	item_name = "Test Item",
	amount = "2500",
	seller_lastname = "Seller",
	seller_firstname = "Sally",
	buyer_lastname = "Buyer",
	buyer_firstname = "Bobby",
	buyer_country = "AUS",
	seller_country = "USA",
	seller_email = "sally.seller@promisepay.com",
	buyer_email = "bobby.buyer@promisepay.com",
	fee_ids = "",
	payment_type_id = "2"		
};
```
#####Example 2 - Request session token
The below example shows the request for a marketplace that passes the Item and User IDs.

```cs
var repo = container.Resolve<ITokenRepository>();
var session_token = new Token {
	current_user_id = "seller1234",
	item_name = "Test Item",
	amount = "2500",
	seller_lastname = "Seller",
	seller_firstname = "Sally",
	buyer_lastname = "Buyer",
	buyer_firstname = "Bobby",
	buyer_country = "AUS",
	seller_country = "USA",
	seller_email = "sally.seller@promisepay.com",
	buyer_email = "bobby.buyer@promisepay.com",
	external_item_id = "TestItemId1234",
	external_seller_id = "seller1234",
	external_buyer_id = "buyer1234",
	fee_ids = "",
	payment_type_id = "2"		
};
```
##Items

#####Create an item
```cs
var repo = container.Resolve<IItemRepository>();
var id = Guid.NewGuid().ToString();
var buyerId = "ec9bf096-c505-4bef-87f6-18822b9dbf2c"; //some user created before
var sellerId = "fdf58725-96bd-4bf8-b5e6-9b61be20662e"; //some user created before
var item = new Item
{
	Id = id,
	Name = "Test Item #1",
	Amount = 1000,
	PaymentType = PaymentType.Express,
	BuyerId = buyerId, //optional field
	SellerId = sellerId, //optional field
	//No fee at this stage, optional field
	Description = "Test item #1 description"
};
var createdItem = repo.CreateItem(item);
```

#####Get an item
```cs
var repo = container.Resolve<IItemRepository>();
var id = "36aa17fb-5ea6-432b-8363-8074ae02603d";
var gotItem = repo.GetItemById(id);
```

#####Get a list of items
```cs
var repo = container.Resolve<IItemRepository>();
var items = repo.ListItems();
```

#####Update an item
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var buyerId = "ec9bf096-c505-4bef-87f6-18822b9dbf2c"; //some user created before
var sellerId = "fdf58725-96bd-4bf8-b5e6-9b61be20662e"; //some user created before
var item = new Item
{
	Id = id,
	Name = "Test Item #1",
	Amount = 1000,
	PaymentType = PaymentType.Express,
	BuyerId = buyerId, //optional field
	SellerId = sellerId, //optional field
	//No fee at this stage, optional field
	Description = "Test item #1 description"
};

var updatedItem = repo.UpdateItem(item);

```
#####Delete an item
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var result = repo.DeleteItem(id)
```

#####Get an item status
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var status = repo.GetStatusForItem(id);
```

#####Get an item's buyer
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var buyer = repo.GetBuyerForItem(id);
```

#####Get an item's seller
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var seller = repo.GetSellerForItem(id);
```

#####Get an item's fees
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var fees = repo.ListFeesForItem(id);
```

#####Get an item's transactions
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var transactions = repo.ListTransactionsForItem(id);
```
#####Get an item's wire details
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var wireDetails = repo.GetWireDetailsForItem(id);
```

#####Get an item's BPAY details
```cs
var repo = container.Resolve<IItemRepository>();
var id = "bb2323cf-4838-4fcb-a288-933d0307523d";
var bPayDetails = repo.GetBPayDetailsForItem(id);
```

##Users

#####Create a user

```cs
var repo = container.Resolve<IUserRepository>();

var id = Guid.NewGuid().ToString();
var user = new User
{
    Id = id,
    FirstName = "Test",
    LastName = "Test",
    City = "Test",
    AddressLine1 = "Line 1",
    Country = "AUS",
    State = "state",
    Zip = "123456",
    Email = id + "@google.com"
};

var createdUser = repo.CreateUser(user);	
```

#####Get a user

```cs
var repo = container.Resolve<IUserRepository>();
var id = "871f83ce-c55d-43ce-ba97-c65628d041a9";

var user = repo.GetUserById(id);
```
#####Update a user

```cs
var repo = container.Resolve<IUserRepository>();
var id = "871f83ce-c55d-43ce-ba97-c65628d041a9";

var user = new User
{
    Id = id,
    FirstName = "Test",
    LastName = "Test",
    City = "Test",
    AddressLine1 = "Line 1",
    Country = "AUS",
    State = "state",
    Zip = "123456",
    Email = id + "@google.com"
};

var updatedUser = repo.UpdateUser(user);	
```

#####Get a list of users

```cs
	var repo = container.Resolve<IUserRepository>();
	var users = repo.ListUsers();
```

#####Delete a User

```cs
	var repo = container.Resolve<IUserRepository>();
	var id = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	repo.DeleteUser(id);
```

#####Get a user's card accounts

```cs
	var repo = container.Resolve<IUserRepository>();
	var id = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var accounts = repo.ListCardAccountsForUser(id);	
```

#####Get a user's PayPal accounts

```cs
	var repo = container.Resolve<IUserRepository>();
	var id = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var accounts = repo.ListPayPalAccountsForUser(id);
```

#####Get a user's bank accounts

```cs
	var repo = container.Resolve<IUserRepository>();
	var id = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var accounts = repo.ListBankAccountsForUser(id);	
```

#####Get a user's items

```cs
	var repo = container.Resolve<IUserRepository>();
	var id = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var items = repo.ListItemsForUser(id);
```

#####Set a user's disbursement account

```cs
	var repo = container.Resolve<IUserRepository>();
	var userId = "871f83ce-c55d-43ce-ba97-c65628d041a9";	
	var accountId = "d077620f-f207-451c-abea-9ed430ea2cbf";	
	bool result = repo.SetDisbursementAccount(userId, accountId);
```

##Item Actions
#####Make payment
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var accountId = "d077620f-f207-451c-abea-9ed430ea2cbf";
	var item = repo.MakePayment(itemId, accountId);
```

#####Request payment
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var item = repo.RequestPayment(itemId);
```

#####Release payment
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var releaseAmount = 123;
	var item = repo.ReleasePayment(itemId, releaseAmount);
```

#####Request release
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var releaseAmount = 123;
	var item = repo.RequestRelease(itemId, releaseAmount);
```

#####Cancel
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var item = repo.Cancel(itemId);
```

#####Acknowledge wire
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var item = repo.AcknowledgeWire(itemId);
```

#####Acknowledge PayPal
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var item = repo.AcknowledgePayPal(itemId);
```

#####Revert wire
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var item = repo.RevertWire(itemId);
```

#####Request refund
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var refundAmount = 123;
	var refundMessage = "refund message";
	var item = repo.RequestRefund(itemId, refundAmount, refundMessage);
```

#####Refund
```cs
	var repo = container.Resolve<IItemRepository>();
	var itemId = "871f83ce-c55d-43ce-ba97-c65628d041a9";
	var refundAmount = 123;
	var refundMessage = "refund message";
	var item = repo.Refund(itemId, refundAmount, refundMessage);
```

##Card Accounts
#####Create a card account
```cs
var repo = container.Resolve<ICardAccountRepository>();
var userId = "ec9bf096-c505-4bef-87f6-18822b9dbf2c"; //some user created before
var account = new CardAccount
{
	UserId = userId,
	Active = true,
	Card = new Card
	{
		FullName = "Batman",
		ExpiryMonth = "11",
		ExpiryYear = "2020",
		Number = "4111111111111111",
		Type = "visa",
		CVV = "123"
	}
};
var createdAccount = repo.CreateCardAccount(account);
var id = createdAccount.Id;
```

#####Get a card account
```cs
var repo = container.Resolve<ICardAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var gotAccount = repo.GetCardAccountById(accountId);
```

#####Delete a card account
```cs
var repo = container.Resolve<ICardAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var result = repo.DeleteCardAccount(accountId); //result should be true
var gotAccount = repo.GetCardAccountById(accountId); //gotAccount.Active should be false
```

#####Get a card account's users
```cs
var repo = container.Resolve<ICardAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var gotUser = repo.GetUserForCardAccount(accountId);
```

##Bank Accounts
#####Create a bank account
```cs
var repo = container.Resolve<IBankAccountRepository>();
var userId = "ec9bf096-c505-4bef-87f6-18822b9dbf2c"; //some user created before
var account = new BankAccount
{
	UserId = userId,
	Active = true,
	Bank = new Bank
	{
		BankName = "Test bank, inc",
		AccountName = "Test account",
		AccountNumber = "8123456789",
		AccountType = "savings",
		Country = "AUS",
		HolderType = "personal",
		RoutingNumber = "123456"
	}
};
var createdAccount = repo.CreateBankAccount(account);
```

#####Get a bank account
```cs
var repo = container.Resolve<IBankAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var gotAccount = repo.GetBankAccountById(accountId);
```

#####Delete a bank account
```cs
var repo = container.Resolve<IBankAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var result = repo.DeleteBankAccount(accountId); //result should be true
var gotAccount = repo.GetBankAccountById(accountId); //gotAccount.Active should be false
```

#####Get a bank account's users
```cs
var repo = container.Resolve<IBankAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var gotUser = repo.GetUserForBankAccount(accountId);
```

##PayPal Accounts
#####Create a PayPal account
```cs
var repo = container.Resolve<IPayPalAccountRepository>();
var userId = "ec9bf096-c505-4bef-87f6-18822b9dbf2c"; //some user created before
var account = new PayPalAccount
{
	UserId = userId,
	Active = true,
	PayPal = new PayPal
	{
		Email = "aaa@bbb.com"
	}
};
var createdAccount = repo.CreatePayPalAccount(account);
```
#####Get a PayPal account
```cs
var repo = container.Resolve<IPayPalAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var gotAccount = repo.GetPayPalAccountById(accountId);
```
#####Delete a PayPal account
```cs
var repo = container.Resolve<IPayPalAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var result = repo.DeletePayPalAccount(accountId); //result should be true
var gotAccount = repo.GetPayPalAccountById(accountId); //gotAccount.Active should be false
```

#####Get a PayPal account's users
```cs
var repo = container.Resolve<IPayPalAccountRepository>();
var accountId = "14a74a3c-8358-4c99-bcf2-4c6ed7454747";
var gotUser = repo.GetUserForPayPalAccount(accountId);
```
##Companies

#####Create a company
```cs
var repo = container.Resolve<ICompanyRepository>();
var userId = "ec9bf096-c505-4bef-87f6-18822b9dbf2c"; //some user created before
var company = new Company
{
	UserId = userId,
	Name = "Acme Co",
	LegalName = "Acme Co Pty Ltd",
	TaxNumber = "1231231",
	ChargeTax = true,
	AddressLine1 = "123 Test St",
	AddressLine2 = "",
	City = "Melbourne",
	State = "VIC",
	Zip = "3000",
	Country = "AUS",
};
var createdCompany = repo.CreateCompany(company);
```

#####Get a company
```cs
var repo = container.Resolve<ICompanyRepository>();
var id = "36aa17fb-5ea6-432b-8363-8074ae02603d";
var gotItem = repo.GetCompanyById(id);
```

#####Get a list of companies
```cs
var repo = container.Resolve<ICompanyRepository>();
var companies = repo.ListCompanies();
```

#####Update a company
```cs
var repo = container.Resolve<ICompanyRepository>();
var id = "ec9bf096-c505-4bef-87f6-18822b9dbf2c";
var userId = "ec9bf096-c505-4bef-87f6-18822b9dbf2c"; //some user created before
var company = new Company
{
	Id = id,
	Name = "Acme Co",
	LegalName = "Acme Co Pty Ltd",
	TaxNumber = "1231231",
	ChargeTax = true,
	AddressLine1 = "123 Test St",
	AddressLine2 = "",
	City = "Melbourne",
	State = "VIC",
	Zip = "3000",
	Country = "AUS",
};

var updatedCompany = repo.UpdateCompany(company);
```

##Fees
#####Get a list of fees
```cs
var repo = container.Resolve<IFeeRepository>();
var fees = repo.ListFees();
```

#####Get a fee
```cs
var repo = container.Resolve<IFeeRepository>();
var id = "79116c9f-d750-4faa-85c7-b7da36f23b38";
var fee = repo.GetFeeById(id);
```

#####Create a fee
```cs
var repo = container.Resolve<IFeeRepository>();
var feeId = Guid.NewGuid().ToString();
var createdFee = repo.CreateFee(new Fee
{
	Id = feeId,
	Amount = 1000,
	Name = "Test fee #1",
	FeeType = FeeType.Fixed,
	Cap = "1",
	Max = "3",
	Min = "2",
	To = "buyer"
});
```

##Transactions
#####Get a list of transactions
```cs
var repo = container.Resolve<ITransactionRepository>();
var transactions = repo.ListTransactions();
```

#####Get a transactions
```cs
var repo = container.Resolve<ITransactionRepository>();
var id = "79116c9f-d750-4faa-85c7-b7da36f23b38";
var transaction = repo.GetTransaction(id);
```

#####Get a transaction's users
```cs
var repo = container.Resolve<ITransactionRepository>();
var id = "79116c9f-d750-4faa-85c7-b7da36f23b38";
var user = repo.GetUserForTransaction(id);
```

#####Get a transaction's fees
```cs
var repo = container.Resolve<ITransactionRepository>();
var id = "79116c9f-d750-4faa-85c7-b7da36f23b38";
var fee = repo.GetFeeForTransaction(id);
```

#4. Contributing
	1. Fork it ( https://github.com/PromisePay/promisepay-dotnet/fork )
	2. Create your feature branch (`git checkout -b my-new-feature`)
	3. Commit your changes (`git commit -am 'Add some feature'`)
	4. Push to the branch (`git push origin my-new-feature`)
	5. Create a new Pull Request
