# Spring Boot 数据库 CRUD 操作

Spring Boot 中常用的三种数据库操作方式：JPA、MyBatis、JdbcTemplate。

## 方式一：Spring Data JPA

### 实体定义

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String name;

    private Integer age;

    @Column(unique = true)
    private String email;

    @CreationTimestamp
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;
}
```

### Repository 接口

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // 方法命名规则自动生成查询
    User findByName(String name);
    List<User> findByAgeGreaterThan(Integer age);
    List<User> findByNameContaining(String keyword);
    List<User> findByAgeBetween(Integer min, Integer max);
    List<User> findByAgeIn(List<Integer> ages);

    // 排序和分页
    List<User> findByAgeGreaterThanOrderByAgeDesc(Integer age);
    Page<User> findByAgeGreaterThan(Integer age, Pageable pageable);

    // 自定义 JPQL
    @Query("SELECT u FROM User u WHERE u.email LIKE %:domain")
    List<User> findByEmailDomain(@Param("domain") String domain);

    // 原生 SQL
    @Query(value = "SELECT * FROM users WHERE age > :age", nativeQuery = true)
    List<User> findByAgeNative(@Param("age") Integer age);

    // 更新
    @Modifying
    @Query("UPDATE User u SET u.name = :name WHERE u.id = :id")
    int updateName(@Param("id") Long id, @Param("name") String name);
}
```

### Service 层

```java
@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository userRepository;

    // 创建
    public User create(User user) {
        return userRepository.save(user);
    }

    // 批量创建
    public List<User> createAll(List<User> users) {
        return userRepository.saveAll(users);
    }

    // 查询单条
    public User getById(Long id) {
        return userRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("用户不存在"));
    }

    // 查询全部
    public List<User> listAll() {
        return userRepository.findAll();
    }

    // 分页查询
    public Page<User> listPage(int page, int size) {
        return userRepository.findAll(PageRequest.of(page, size, Sort.by("age").descending()));
    }

    // 更新
    public User update(Long id, User user) {
        User existing = getById(id);
        existing.setName(user.getName());
        existing.setAge(user.getAge());
        return userRepository.save(existing);
    }

    // 删除
    public void delete(Long id) {
        userRepository.deleteById(id);
    }
}
```

## 方式二：MyBatis

### Mapper 接口

```java
@Mapper
public interface UserMapper {

    @Insert("INSERT INTO users(name, age, email) VALUES(#{name}, #{age}, #{email})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    int insert(User user);

    @Select("SELECT * FROM users WHERE id = #{id}")
    User selectById(Long id);

    @Select("SELECT * FROM users WHERE age > #{age}")
    List<User> selectByAge(Integer age);

    @Update("UPDATE users SET name=#{name}, age=#{age} WHERE id=#{id}")
    int update(User user);

    @Delete("DELETE FROM users WHERE id = #{id}")
    int deleteById(Long id);

    // 动态 SQL（XML 方式更灵活）
    List<User> selectByCondition(UserQuery query);
}
```

### XML 动态 SQL（UserMapper.xml）

```xml
<select id="selectByCondition" resultType="User">
    SELECT * FROM users
    <where>
        <if test="name != null and name != ''">
            AND name LIKE CONCAT('%', #{name}, '%')
        </if>
        <if test="minAge != null">
            AND age >= #{minAge}
        </if>
        <if test="maxAge != null">
            AND age &lt;= #{maxAge}
        </if>
    </where>
    ORDER BY id DESC
</select>

<insert id="batchInsert">
    INSERT INTO users(name, age, email) VALUES
    <foreach collection="list" item="item" separator=",">
        (#{item.name}, #{item.age}, #{item.email})
    </foreach>
</insert>
```

## 方式三：JdbcTemplate

```java
@Repository
@RequiredArgsConstructor
public class UserDao {

    private final JdbcTemplate jdbc;

    public int insert(User user) {
        return jdbc.update("INSERT INTO users(name, age, email) VALUES(?, ?, ?)",
                user.getName(), user.getAge(), user.getEmail());
    }

    public User findById(Long id) {
        return jdbc.queryForObject("SELECT * FROM users WHERE id = ?",
                (rs, rowNum) -> {
                    User u = new User();
                    u.setId(rs.getLong("id"));
                    u.setName(rs.getString("name"));
                    u.setAge(rs.getInt("age"));
                    u.setEmail(rs.getString("email"));
                    return u;
                }, id);
    }

    public List<User> findAll() {
        return jdbc.query("SELECT * FROM users",
                (rs, rowNum) -> {
                    User u = new User();
                    u.setId(rs.getLong("id"));
                    u.setName(rs.getString("name"));
                    u.setAge(rs.getInt("age"));
                    return u;
                });
    }

    public int update(User user) {
        return jdbc.update("UPDATE users SET name=?, age=? WHERE id=?",
                user.getName(), user.getAge(), user.getId());
    }

    public int delete(Long id) {
        return jdbc.update("DELETE FROM users WHERE id = ?", id);
    }
}
```

## 说明

| 方式 | 特点 | 适用场景 |
| --- | --- | --- |
| **JPA** | 自动建表、方法名生成查询、少写SQL | 简单CRUD、快速开发 |
| **MyBatis** | SQL完全可控、动态SQL灵活 | 复杂查询、SQL优化需求 |
| **JdbcTemplate** | 轻量、原生SQL、无ORM开销 | 简单项目、性能敏感场景 |
