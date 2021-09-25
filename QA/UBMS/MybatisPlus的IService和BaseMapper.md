| IService                                                     | 注释                      | BaseMapper                                       | 注释                                                         |
| ------------------------------------------------------------ | ------------------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| 进一步封装：采用 get 查询单行, remove 删除, list 查询集合, page 分页 |                           | DDML                                             |                                                              |
| boolean save(T entity);                                      | 选择字段，策略插入        | int insert(T entity);                            | 插入一条记录                                                 |
| boolean saveBatch(Collection entityList);                    | 批量插入                  |                                                  |                                                              |
| boolean saveOrUpdateBatch(Collection entityList);            |                           |                                                  |                                                              |
| boolean removeById(Serializable id);                         |                           | int deleteById(Serializable id);                 | 根据 ID 删除                                                 |
| boolean remove(Wrapper queryWrapper);v                       | 无对应记录也返回true      | int delete(Wrapper wrapper);                     | 根据 entity 条件删除                                         |
| boolean removeByMap(Map<String, Object> columnMap);          | columnMap 表字段 map 对象 | int deleteByMap(Map<String, Object> columnMap);  | 根据 columnMap 条件，删除记录                                |
|                                                              |                           | int deleteBatchIds(List<T>)                      |                                                              |
|                                                              |                           | int update(T entry,Wrapper wrapper)              |                                                              |
| boolean updateById(T entity);                                |                           | int updateById(T entity);                        | 根据 ID 修改                                                 |
| boolean saveOrUpdate(T entity);                              |                           |                                                  |                                                              |
| T getById(Serializable id);                                  |                           | T selectById(Serializable id);                   | 根据 ID 查询                                                 |
| Collection listByMap(Map<String, Object> columnMap);         |                           | List selectByMap(Map<String, Object> columnMap); | 查询（根据 columnMap 条件）                                  |
| T getOne(Wrapper queryWrapper);                              | 有多个取一个              | T selectOne(Wrapper queryWrapper);               | 根据 entity 条件，查询一条记录，如果逻辑非唯一需要 wrapper.last("limit 1") 设置唯一性 |

> `selectbyMap()`这个查询方法，可以这么理解，把字段名和字段值放在一个map中（记得这里的字段是数据库中的，而不是类中的）通过这个条件去查询
