DwimDao
=======

Proof of concept how to "auto-implement" a DAO defined as a interface (DwimDao = "Do What I Mean Data Access Object").
The idea is that one just declares the needed methods on a DAO interface, but never needs to implement them:
the DwimDao engine guesses from the method declarations what SQL statement is needed. 
 
This is meant to be vaguely similar to Rails ActiveRecord (http://api.rubyonrails.org/classes/ActiveRecord/Base.html), but in Java.
Currently, this is just a proof of concept. The DAO interface can contain methods named like findBy<First>[And<Field>]* that can return
the stored value object or a collection of them. The DwimDAO will guess the actual SQL from the method names. For cases where
this guessing is not possible, one can give the SQL in a @DwimSQL annotation. For example:

public interface UserDao {
	User findById(Long id);
	Collection<User> findByFirstName(String firstName);
	Collection<User> findByFirstNameAndSecondName(String firstName, String secondName);
	
	@DwimSQL("select * from user where secondname like ?")
	Collection<User> findBySecondNameLike(String secondnamepattern);
}

You create a DAO like this:

UserDao dao = DwimDao.make(UserDao.class, datasource);

and the finder methods on the UserDao interface can be used without ever being explicitly implemented. Compare
https://github.com/stoerr/DwimDao/tree/master/src/test/java/net/stoerr/dwimdao/test for a simple example.

Ideas for extension
-------------------

To make this really usable one would need to implement the following things.
- Implement non-bean Types like Strings and numbers for @DwimSQL
- Implement "save", "delete", "deleteBy" methods
- What to do about update methods?
- Provide Annotations for:
  * perhaps the table name at class level
  * an explicitly provided SQL statement at method level
  * perhaps a special row mapper class at class level (currently ParameterizedBeanPropertyRowMapper is used)
- Integration with Spring: a way to specify / inject the datasource and / or a JdbcTemplate.
- Rethink exception handling, especially for findBy single bean methods.

Probably I should study ActiveRecord and the Groovy equivalent to find ideas. :-)
