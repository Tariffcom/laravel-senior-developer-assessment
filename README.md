<h1 align="center">
Assessment for Senior Laravel Developer
</h1>

**Output**: All tasks should be committed to a Github repo, using best practices for commits. Once complete, please email a link to the repository to mbowyer@tariffcom.com. If you have created a private repository, please add mchlbwyr as a contributor.

## Level 1

### Goals

- [ ] Implement Laravel’s default login features using Laravel Breeze
- [ ] Develop User CRUD functionalities using Tailwind styling
- [ ] Add Validation rules to the User CRUD

### Prerequisites

Base the user migration file on the following table:

```mysql
mysql> show columns from users;
+-------------------+-----------------+------+-----+---------+----------------+
| Field             | Type            | Null | Key | Default | Extra          |
+-------------------+-----------------+------+-----+---------+----------------+
| id                | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| prefixname        | varchar(255)    | YES  |     | NULL    |                |
| firstname         | varchar(255)    | NO   |     | NULL    |                |
| middlename        | varchar(255)    | YES  |     | NULL    |                |
| lastname          | varchar(255)    | NO   |     | NULL    |                |
| suffixname        | varchar(255)    | YES  |     | NULL    |                |
| email             | varchar(255)    | NO   | UNI | NULL    |                |
| password          | varchar(255)    | NO   |     | NULL    |                |
| photo             | text            | YES  |     | NULL    |                |
| type              | varchar(255)    | YES  | MUL | user    |                |
| remember_token    | varchar(100)    | YES  |     | NULL    |                |
| email_verified_at | timestamp       | YES  |     | NULL    |                |
| created_at        | timestamp       | YES  |     | NULL    |                |
| updated_at        | timestamp       | YES  |     | NULL    |                |
| deleted_at        | timestamp       | YES  |     | NULL    |                |
+-------------------+-----------------+------+-----+---------+----------------+
```

<br>

### Instructions

1. Start a new project in Laravel 10
1. Implement the default login feature using the [laravel/breeze](https://laravel.com/docs/10.x/starter-kits#laravel-breeze) package using the Inertia stack with Vue.
1. Add a page to list all users (users.index) in a table.
1. Add a page to display a single user (users.show).
1. Add a page to display the form to create a new user (users.create).
1. Add a page to edit a user (users.edit / users.update).
1. Add a button to delete a user (users.destroy).
1. Add a page to list all soft deleted users (users.trashed).
1. Add a button to restore a soft deleted user (users.restore).
1. Add a button to permanently delete a soft deleted user (users.delete).

<br>

### ✎ Notes
- All users routes must implement the `auth` middleware.
- The value for `prefixname` should only be one in `[Mr, Mrs, Ms]`.
- Use your best judgment in declaring the validation rules for the fields.
- Use TailwindCSS to style any pages and components you create.
<br>
<br>
<br>

## Level 2

### Goals

- [ ] Generate a table called <code>details</code> to save additional user background information
- [ ] Implement an event called <code>UserSaved</code> triggered when a user is created or updated
- [ ] Implement a listener that auto-saves additional user details when the <code>UserSaved</code> event is triggered

### Instructions

1. Create a migration file for a table called `details`. Use the following table for reference on the columns:
	```mysql
	mysql> show columns from details;
	+------------+-----------------+------+-----+---------+----------------+
	| Field      | Type            | Null | Key | Default | Extra          |
	+------------+-----------------+------+-----+---------+----------------+
	| id         | bigint unsigned | NO   | PRI | NULL    | auto_increment |
	| key        | varchar(255)    | NO   | MUL | NULL    |                |
	| value      | text            | YES  |     | NULL    |                |
	| icon       | varchar(255)    | YES  |     | NULL    |                |
	| status     | varchar(255)    | NO   |     | 1       |                |
	| type       | varchar(255)    | YES  |     | detail  |                |
	| user_id    | bigint unsigned | YES  | MUL | NULL    |                |
	| created_at | timestamp       | YES  |     | NULL    |                |
	| updated_at | timestamp       | YES  |     | NULL    |                |
	+------------+-----------------+------+-----+---------+----------------+
	```
1. Generate an Eloquent Model file `app/Detail.php`.
1. Assign a one-to-many relationship between `App\User` model and `App\Detail` model.
1. Create an Event and listener on the `App\User` Eloquent Model's `saved` event.
	1. The listener should save to a table called `details` the following information:
		1. The user's full name based on `firstname`, `middlename`, and `lastname`.
		1. The user's middle initial based on abbreviating the `middlename`.
		1. The user's avatar based on a given `photo`
		1. The user's gender based on the value of `prefixname`.
		<details>
		<summary>Example data:</summary>

        For a user with attributes of:
		```php
		{
			id: 1,
			prefixname: "Mr.",
			firstname: "Juan",
			middlename: "Palito",
			lastname: "dela Cruz",
			suffixname: "Jr.",
			email: "juan@demo.ph",
			photo: null,
			type: "user",
		}
		```

		The saved data should be:

		```mysql
		mysql> select `id`,`key`,`value`,`type`,`user_id` from details;
		+----+-----------------+-------------------+--------+---------+
		| id | key             | value             | type   | user_id |
		+----+-----------------+-------------------+--------+---------+
		|  1 | Full name       | Juan P. dela Cruz | bio    |       1 |
		|  2 | Middle Initial  | P.                | bio    |       1 |
		|  3 | Avatar          | http://localho... | bio    |       1 |
		|  4 | Gender          | Male              | bio    |       1 |
		+----+-----------------+-------------------+--------+---------+
		```
		</details>

<br>

### ✎ Notes

- The column `details.user_id` must be a foreign key that references `users.id` and cascades on `DELETE` and `UPDATE`
