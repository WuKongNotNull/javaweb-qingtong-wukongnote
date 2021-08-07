# src.database.properities配置文件存储数据库信息

```properties
jdbc.driver_class=com.mysql.jdbc.Driver
jdbc.connection.url=jdbc:mysql://127.0.0.1:3306/数据库
jdbc.connection.username=root
jdbc.connection.password=root
```

# 配合util.ConfigManager.java连接数据库

```java
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

//读取配置文件工具类--单例模式（有且仅有一个实例）
public class ConfigManager {

	//读取配置文件Properties.load(inputstream)
	
	private static ConfigManager configManager;
	private static Properties properties;
	
	//构造方法(把配置文件load到properties)
	private ConfigManager(){
		
		String configFile = "database.properties";
		properties = new Properties();
		//通过classpath获取资源 ，通过当前类所在包的根目录下去查找资源文件
		InputStream is = ConfigManager.class.getClassLoader().getResourceAsStream(configFile);
		
		try {
			properties.load(is);
			is.close();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
	}
	
	//提供一个全局的访问点
	public static ConfigManager getInstance(){
		if(configManager == null){
			configManager = new ConfigManager();
		}
		return configManager;
	}
	
	
	
	public String getString(String key){
		return properties.getProperty(key);
		
	}
	
		
}

```

# 修改getConnection（）方法

```java
	// 获取数据库连接
	public boolean getConnection() {
		// 读出配置信息
		String driver = ConfigManager.getInstance().getString("jdbc.driver_class");
		String url = ConfigManager.getInstance().getString("jdbc.connection.url");
		String username = ConfigManager.getInstance().getString("jdbc.connection.username");
		String password = ConfigManager.getInstance().getString("jdbc.connection.password");
		// 加载JDBC驱动
		try {
			Class.forName(driver);
			// 与数据库建立连接
			conn = DriverManager.getConnection(url, username, password);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
			return false;
		} catch (SQLException e) {
			e.printStackTrace();
			return false;
		}
		return true;
	}
```



# 懒汉模式-读取配置文件

```java
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

//读取配置文件工具类--单例模式（有且仅有一个实例）
public class ConfigManager {

	//读取配置文件Properties.load(inputstream)
	private static ConfigManager configManager;
	private static Properties properties;
	
	//构造方法(把配置文件load到properties)
	private ConfigManager(){
		String configFile = "database.properties";
		properties = new Properties();
		//通过classpath获取资源 ，通过当前类所在包的根目录下去查找资源文件
		InputStream is = ConfigManager.class.getClassLoader().getResourceAsStream(configFile);
		try {
			properties.load(is);
			is.close();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	/*
	 * 线程不安全，在并发环境下，很有可能会出现多个configManager实例
	 * 需要考虑同步，可以采用synchronized
	 * 懒汉模式
	 */
	//提供一个全局的访问点
	public static synchronized ConfigManager getInstance(){
		if(configManager == null){
			configManager = new ConfigManager();
		}
		return configManager;
	}
	public String getString(String key){
		return properties.getProperty(key);
		
	}
}
```

# 饿汉模式-读取配置文件

```java
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

//读取配置文件工具类--单例模式（有且仅有一个实例）
//饿汉模式
public class ConfigManager {
	//类加载的时候，自行进行初始化操作
	private static ConfigManager configManager = new ConfigManager();
	// 饿汉模式（变种）
	/*private static ConfigManager configManager;
	static {
		configManager = new ConfigManager();
	}*/
	//读取配置文件Properties.load(inputstream)
	private static Properties properties;
	
	//构造方法(把配置文件load到properties)
	private ConfigManager(){
		String configFile = "database.properties";
		properties = new Properties();
		//通过classpath获取资源 ，通过当前类所在包的根目录下去查找资源文件
		InputStream is = ConfigManager.class.getClassLoader().getResourceAsStream(configFile);
		try {
			properties.load(is);
			is.close();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	//提供一个全局的访问点
	public static ConfigManager getInstance(){
		return configManager;
	}
	
	public String getString(String key){
		return properties.getProperty(key);
	}
	
}
```

