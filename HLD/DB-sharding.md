[back to previous page](./HLD.md)

---

# DB Horizontal Scaling - SHARDING

**Sharding** - separating large DB into smaller, more easily managed parts called SHARDS.Each Shard shares same schema, actual data on each shard is uinque to the shard.

**Sharding key** - aka partition key, consists of >=1 cols that determine how data is dsitributed, important that you choose a sharding key that ends up dsitributing data across shards evenly.

Challanges posed by Sharding : 
- **Resharding Data** - required when single shard size becomes too much and needs further division OR some shards experience too much traffic sue to un-even distribution. Needs updating sharding function and moving data around - **CONSISTENT HASHING** helps with this 

- **Celebrity Problem** - aka hotspot key problem, excessive access to a specific shard could cause server overload. Solution - allocate >=1 shard per celebrity as per the load

- **Join and de-normalization** - once sharded across multiple serves it is hard to perform join operations across DB shards. Common workaround is to de-normalize the DB 
   - *Consider a normalized database with two tables: students and branch. The students table has attributes roll_no, stud_name, age, and branch_id, while the branch table has attributes branch_id and branch_name. To retrieve student names along with their branch names, a join operation is required. In a denormalized database, the branch_name can be added directly to the students table, eliminating the need for a join*