
# SQLalchemy 创建表

sqlalchemy创建表
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
 
"""
http://www.cnblogs.com/wupeiqi/articles/5699254.html

SQLAlchemy只能创建和删除表，不能修改表结构（要修改表结构要借助第三方插件）
"""

from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import create_engine, Column, Integer, String, ForeignKey, UniqueConstraint, Index

# 创建引擎，使用pymysql连接数据库
engine = create_engine("mysql+pymysql://root:123456@192.168.1.22:3306/sqlalchemytest?charset=utf8", max_overflow=5)

Base = declarative_base()  # 创建一个sqlalchemy基础类，下面所有表类都要继承它


# 创建单表
class Users(Base):  # 继承Base
    __tablename__ = 'users'   # 表名
    # 创建数据库字段
    id = Column(Integer, primary_key=True)
    name = Column(String(32))
    extra = Column(String(16))

    # 创建联合索引
    __table_args__ = (
        UniqueConstraint('id', 'name', name='uix_id_name'),
        Index('ix_id_name', 'name', 'extra'),
    )



# 一对多
class Favor(Base):
    __tablename__ = 'favor'
    nid = Column(Integer, primary_key=True)
    caption = Column(String(50), default='red', unique=True)


class Person(Base):
    __tablename__ = 'person'
    nid = Column(Integer, primary_key=True)
    name = Column(String(32), index=True, nullable=True)
    favor_id = Column(Integer, ForeignKey("favor.nid"))





# 多对多
class Group(Base):
    __tablename__ = 'group'
    id = Column(Integer, primary_key=True)
    name = Column(String(64), unique=True, nullable=False)


class Server(Base):
    __tablename__ = 'server'

    id = Column(Integer, primary_key=True, autoincrement=True)
    hostname = Column(String(64), unique=True, nullable=False)
    port = Column(Integer, default=22)


class ServerToGroup(Base):
    __tablename__ = 'servertogroup'
    nid = Column(Integer, primary_key=True, autoincrement=True)
    server_id = Column(Integer, ForeignKey('server.id'))
    group_id = Column(Integer, ForeignKey('group.id'))


def init_db():
    Base.metadata.create_all(engine)  # 查找Base的所有子类并根据这些子类创建表


def drop_db():
    Base.metadata.drop_all(engine)     # 删除根据Base所有子类创建的表

if __name__ == '__main__':
    init_db()
    # drop_db()

```

---

# sqlalchemy操作表

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""
http://www.cnblogs.com/wupeiqi/articles/5699254.html
"""
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, ForeignKey, UniqueConstraint, Index
from sqlalchemy.orm import sessionmaker, relationship
from sqlalchemy import create_engine

# 创建引擎，使用pymysql连接数据库
engine = create_engine("mysql+pymysql://root:123456@192.168.1.22:3306/sqlalchemytest?charset=utf8", max_overflow=5)

Base = declarative_base()  # 创建一个sqlalchemy基础类，下面所有表类都要继承它


# 创建单表
class Users(Base):  # 继承Base
    __tablename__ = 'users'   # 表名
    # 创建数据库字段
    id = Column(Integer, primary_key=True)
    name = Column(String(32))
    extra = Column(String(16))

    # 创建联合索引
    __table_args__ = (
        UniqueConstraint('id', 'name', name='uix_id_name'),
        Index('ix_id_name', 'name', 'extra'),
    )


# 一对多
class Favor(Base):
    __tablename__ = 'favor'
    nid = Column(Integer, primary_key=True)
    caption = Column(String(50), default='red', unique=True)


class Person(Base):
    __tablename__ = 'person'
    nid = Column(Integer, primary_key=True)
    name = Column(String(32), index=True, nullable=True)
    favor_id = Column(Integer, ForeignKey("favor.nid"))


# 多对多
class Group(Base):
    __tablename__ = 'group'
    id = Column(Integer, primary_key=True)
    name = Column(String(64), unique=True, nullable=False)


class Server(Base):
    __tablename__ = 'server'

    id = Column(Integer, primary_key=True, autoincrement=True)
    hostname = Column(String(64), unique=True, nullable=False)
    port = Column(Integer, default=22)


class ServerToGroup(Base):
    __tablename__ = 'servertogroup'
    nid = Column(Integer, primary_key=True, autoincrement=True)
    server_id = Column(Integer, ForeignKey('server.id'))
    group_id = Column(Integer, ForeignKey('group.id'))


def init_db():
    Base.metadata.create_all(engine)  # 查找Base的所有子类并根据这些子类创建表


def drop_db():
    Base.metadata.drop_all(engine)     # 删除根据Base所有子类创建的表


if __name__ == '__main__':
    # init_db()  #创建表

    # 创建session，通过session操作数据库
    Session = sessionmaker(bind=engine)
    session = Session()

    # 操作数据库
    # 增
    # obj = Users(name='alex', extra='haha')   # 创建一个Users对象（创建一条users表的数据）
    # session.add(obj)   # 将数据添加到表中
    # # 批量添加数据
    # session.add_all([
    #     Users(name='eric', extra='sss'),
    #     Users(name='maco', extra='xxx'),
    # ])
    # session.commit()   # 将修改提交到数据库

    # 删
    # session.query(Users).filter(Users.id > 2, Users.name == 'alex').delete()   # 删除Users中id大于2且name为alex的数据
    # session.commit()   # 将修改提交到数据库

    # 改
    # session.query(Users).filter(Users.id == 2).update({'name': 'eric'})  # 修改Users中id等于2的数据的name值
    # session.query(Users).filter(Users.id > 2).update({Users.name: Users.name + "099"}, synchronize_session=False)   # 可以做字符串拼接
    # session.query(Users).filter(Users.id > 2).update({'num': Users.num + 1}, synchronize_session='evaluate')   # 如果修改数字，可以直接做数字加减
    # session.commit()   # 将修改提交到数据库

    # 查
    ret = session.query(Users).all()   # 获取Users所有数据（结果是一个对象的列表）
    print(session.query(Users))   # 查看命令生成的sql语句是什么
    print(ret[0].name)  # 查看获取的数据第一条的name值
    # ret = session.query(Users.name, Users.extra).all()   # 获取Users的name、extra列的所有数据
    # ret = session.query(Users).filter_by(name='alex').all()    # 获取Users中name为alex的所有数据
    # ret = session.query(Users).filter_by(name='alex').first()   # 获取Users中name为alex的数据的第一条

    # 条件
    ret = session.query(Users).filter_by(name='alex').all()    # fileter_by内部调用filter,fileter_by参数为name,fileter参数为Users.name
    ret = session.query(Users).filter(Users.id > 1, Users.name == 'eric').all()  # id大于1且name为eric
    ret = session.query(Users).filter(Users.id.between(1, 3), Users.name == 'eric').all()   # id在1-3之间，且name为eric
    ret = session.query(Users).filter(Users.id.in_([1, 3, 4])).all()   # id为1,3,4的数据
    ret = session.query(Users).filter(~Users.id.in_([1, 3, 4])).all()    # id不为1,3,4的数据
    ret = session.query(Users).filter(Users.id.in_(session.query(Users.id).filter_by(name='eric'))).all()   # 联合查询
    #and、or
    from sqlalchemy import and_, or_

    ret = session.query(Users).filter(and_(Users.id > 3, Users.name == 'eric')).all()
    ret = session.query(Users).filter(or_(Users.id < 2, Users.name == 'eric')).all()
    ret = session.query(Users).filter(
        or_(
            Users.id < 2,
            and_(Users.name == 'eric', Users.id > 3),
            Users.extra != ""
        )).all()

    # 通配符
    ret = session.query(Users).filter(Users.name.like('e%')).all()
    ret = session.query(Users).filter(~Users.name.like('e%')).all()

    # 限制
    ret = session.query(Users)[1:2]

    # 排序
    ret = session.query(Users).order_by(Users.name.desc()).all()
    ret = session.query(Users).order_by(Users.name.desc(), Users.id.asc()).all()

    # 分组
    from sqlalchemy.sql import func

    ret = session.query(Users).group_by(Users.extra).all()
    ret = session.query(
        func.max(Users.id),
        func.sum(Users.id),
        func.min(Users.id)).group_by(Users.name).all()

    ret = session.query(
        func.max(Users.id),
        func.sum(Users.id),
        func.min(Users.id)).group_by(Users.name).having(func.min(Users.id) > 2).all()

    # 连表

    ret = session.query(Users, Favor).filter(Users.id == Favor.nid).all()

    ret = session.query(Person).join(Favor).all()  # 相当于sql语句中的innerjoin

    ret = session.query(Person).join(Favor, isouter=True).all()   # 相当于sql语句中的leftjoin

    # 组合
    q1 = session.query(Users.name).filter(Users.id > 2)
    q2 = session.query(Favor.caption).filter(Favor.nid < 2)
    ret = q1.union(q2).all()   # 联合，有重复自动去重

    q1 = session.query(Users.name).filter(Users.id > 2)
    q2 = session.query(Favor.caption).filter(Favor.nid < 2)
    ret = q1.union_all(q2).all()  # 联合，有重复不去重

```

# sqlalchemy一对多关系

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""
http://www.cnblogs.com/wupeiqi/articles/5699254.html

创建表：
    主动指定约束（外键）
操作表：
    类：指定查询结果的输出形式（__repr__方法）
    单表操作
    连表操作：
            relationship正向、反向查询
            session.query(User).join(Group, isouter=True).all()   默认为innerjoin操作，加了isouter=True就是left join操作
"""
from sqlalchemy import create_engine, Column, Integer, String, ForeignKey
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker,relationship

# 创建数据库连接
engine = create_engine("mysql+pymysql://root:123456@192.168.1.22:3306/sqlalchemytest", max_overflow=5)
# 创建基类
Base = declarative_base()


class Group(Base):
    __tablename__ = 'group'
    id = Column(Integer, primary_key=True, autoincrement=True)
    caption = Column(String(32))

    # def __repr__(self):
    #     return '(%s,%s)' % (self.id, self.caption)

class User(Base):
    __tablename__ = 'user'
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(32))
    group_id = Column(Integer, ForeignKey('group.id'))

    # group与生成表结构无关，仅用于查询方便
    # backref参数是在group表中也创建一个u字段（该字段可自定义），
    # 该字段代指每条group数据对应的所有user信息（是一个user对象的列表）
    group = relationship("Group", backref='u')

    # 查询数据时，默认输出的是一个对象，要想输出更直观，可以自定义输出格式
    # def __repr__(self):
    #     tmp = '(%s,%s,%s)' % (self.id, self.username, self.group_id)
    #     return tmp


# 创建数据库
def init_db():
    Base.metadata.create_all(engine)


# 删除数据库
def drop_db():
    Base.metadata.drop_all(engine)


if __name__ == '__main__':
    # init_db()

    # 创建session用来操作数据库
    Session = sessionmaker(bind=engine)
    session = Session()

    # 添加数据
    # session.add(Group(caption='DBA'))
    # session.add(Group(caption='CEO'))
    # session.commit()

    # session.add_all([
    #     User(username='Alex', group_id=1),
    #     User(username='Eric', group_id=2),
    # ])
    # session.commit()

    # ret = session.query(User).all()  # 查询user表所有信息
    # print(ret)

    # ret = session.query(User).filter(User.username == 'Alex').all()  # 查询user表中username为Alex的所有信息
    # print(ret)

    # ret = session.query(User.username).all()  # 查询user表中所有的username
    # print(ret)

    # ret = session.query(User).join(Group, isouter=True).all()   # 不加isouter=True,默认为inner join，加了就是left join
    # print(session.query(User).join(Group, isouter=True))   # 上面语句的sql形式----该命令取的是User中的数据
    # print(ret)  # 查询结果

    # ret = session.query(User,Group).join(Group, isouter=True).all()   # 不加isouter=True,默认为inner join，加了就是left join
    # print(session.query(User,Group).join(Group, isouter=True))   # 上面语句的sql形式----该命令取的是User和Group表中所有的数据
    # print(ret)  # 查询结果

    # 正向查询：获取user表的username对应的group表的caption----------获取user表数据对应的group信息
    # ret = session.query(User).all()
    # for obj in ret:
    #     # obj代指user表中的每一行数据
    #     # obj.group代指每行数据对应的group数据
    #     print(obj, obj.id, obj.username, obj.group_id, obj.group,obj.group.id, obj.group.caption)

    # 反向查询：获取group表中caption对应的user表中所有的username------获取group表中数据对应的user信息
    ret = session.query(Group).filter(Group.caption=='DBA').first()  # 获取caption为DBA的第一条数据
    # ret.u代指该条数据对应的所有user信息，u字段是在user表中设置的。（group = relationship("Group", backref='u')）
    print(ret.id, ret.caption, ret.u)
    for obj in ret.u:
        # ret.u代指group表中数据对应的所有user信息（是一个列表）
        # obj代指user表中每条数据
        print(obj.username)

```

# sqlalchemy多对多关系-1

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""
http://www.cnblogs.com/wupeiqi/articles/5699254.html

创建表：
    普通表
    额外创建一张关系表，表示表之间的关系，指定约束
操作表：
    类：指定查询结果的输出形式（__repr__方法）
    单表操作
    连表操作：
            relationship正向、反向查询
            session.query(User).join(Group, isouter=True).all()   默认为innerjoin操作，加了isouter=True就是left join操作
"""
from sqlalchemy import create_engine, Column, Integer, String, ForeignKey
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker,relationship

# 创建数据库连接
engine = create_engine("mysql+pymysql://root:123456@192.168.1.22:3306/sqlalchemytest", max_overflow=5)
# 创建基类
Base = declarative_base()


class Host(Base):
    __tablename__ = 'host'
    id = Column(Integer, primary_key=True, autoincrement=True)
    hostname = Column(String(32))
    port = Column(String(32))
    ip = Column(String(32))


class HostUser(Base):
    __tablename__ = 'hostuser'
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(32))


class HostToHostUser(Base):
    __tablename__ = 'host_to_hostuser'
    id = Column(Integer, primary_key=True, autoincrement=True)
    host_id = Column(Integer, ForeignKey('host.id'))
    hostuser_id = Column(Integer, ForeignKey('hostuser.id'))

    host = relationship("Host", backref='h')
    host_user = relationship("HostUser", backref='u')


# 创建数据库
def init_db():
    Base.metadata.create_all(engine)


# 删除数据库
def drop_db():
    Base.metadata.drop_all(engine)

if __name__ == '__main__':
    # init_db()

    # 创建session，用来操作数据库
    Session = sessionmaker(bind=engine)
    session = Session()

    # 添加
    # session.add_all([
    #     Host(hostname='c1', port='22', ip='1.1.1.1'),
    #     Host(hostname='c2', port='22', ip='1.1.1.2'),
    #     Host(hostname='c3', port='22', ip='1.1.1.3'),
    #     Host(hostname='c4', port='22', ip='1.1.1.4'),
    #     Host(hostname='c5', port='22', ip='1.1.1.5'),
    # ])
    # session.commit()

    # session.add_all([
    #     HostUser(username='root'),
    #     HostUser(username='db'),
    #     HostUser(username='nb'),
    #     HostUser(username='sb'),
    # ])
    # session.commit()

    # session.add_all([
    #     HostToHostUser(host_id=1, hostuser_id=1),
    #     HostToHostUser(host_id=1, hostuser_id=2),
    #     HostToHostUser(host_id=1, hostuser_id=3),
    #     HostToHostUser(host_id=2, hostuser_id=2),
    #     HostToHostUser(host_id=2, hostuser_id=3),
    #     HostToHostUser(host_id=2, hostuser_id=4),
    # ])
    # session.commit()

    # 需求：主机c1对应的所有用户------方法一
    # host_obj = session.query(Host).filter(Host.hostname == 'c1').first()  # 获取主机名为c1的主机数据
    # hostuser_id_list = session.query(HostToHostUser.hostuser_id).filter(HostToHostUser.host_id == host_obj.id).all()   # 获取主机c1对应的所有用户id
    # # 获取用户id对应的用户信息---方法1
    # # for item in hostuser_id_list:
    # #     host_user = session.query(HostUser.username).filter(HostUser.id == item[0]).all()
    # #     print(host_user)
    #
    # # 获取用户id对应的用户信息---方法2
    # new_host_id_list = zip(*hostuser_id_list)
    # # print(list(new_host_id_list)[0])
    # host_user = session.query(HostUser.username).filter(HostUser.id.in_(list(new_host_id_list)[0])).all()
    # print(host_user)

    # 需求：主机c1对应的所有用户------方法二
    host_obj = session.query(Host).filter(Host.hostname == 'c1').first()   # 获取主机名为c1的主机数据
    for item in host_obj.h:
        # host_obj.h代指Host表中c1对应HostToHostUser表的所有数据（表的反向查找）
        # item.host_user代指HostToHostUser表中数据对应的HostUser表的信息（正向查找）
        print(item.host_user, item.host_user.id, item.host_user.username)

```

# sqlalchemy多对多关系-2

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""
http://www.cnblogs.com/wupeiqi/articles/5699254.html

创建表：
    普通表
    额外创建一张关系表，表示表之间的关系，指定约束
操作表：
    类：指定查询结果的输出形式（__repr__方法）
    单表操作
    连表操作：
            relationship正向、反向查询
            session.query(User).join(Group, isouter=True).all()   默认为innerjoin操作，加了isouter=True就是left join操作
"""
from sqlalchemy import create_engine, Column, Integer, String, ForeignKey, Table
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker,relationship

# 创建数据库连接
engine = create_engine("mysql+pymysql://root:123456@192.168.1.22:3306/sqlalchemytest", max_overflow=5)
# 创建基类
Base = declarative_base()

# 使用relationship建立与其他表的关系时，若不是在有外键的表中进行创建，多对多表不能使用class类进行创建，而是使用sqlalchemy.Table创建
HostToHostUser = Table('host_to_hostuser', Base.metadata,
                       Column('host_id', ForeignKey("host.id"), primary_key=True),
                       Column('hostuser_id', ForeignKey("hostuser.id"), primary_key=True),
                       )

class Host(Base):
    __tablename__ = 'host'
    id = Column(Integer, primary_key=True, autoincrement=True)
    hostname = Column(String(32))
    port = Column(String(32))
    ip = Column(String(32))

    host_user = relationship('HostUser', secondary=HostToHostUser, backref='u')


class HostUser(Base):
    __tablename__ = 'hostuser'
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(32))


# class HostToHostUser(Base):
#     __tablename__ = 'host_to_hostuser'
#     id = Column(Integer, primary_key=True, autoincrement=True)
#     host_id = Column(Integer, ForeignKey('host.id'))
#     hostuser_id = Column(Integer, ForeignKey('hostuser.id'))

    # host = relationship("Host", backref='h')
    # host_user = relationship("HostUser", backref='u')


# 创建数据库
def init_db():
    Base.metadata.create_all(engine)


# 删除数据库
def drop_db():
    Base.metadata.drop_all(engine)

if __name__ == '__main__':
    # init_db()

    # 创建session，用来操作数据库
    Session = sessionmaker(bind=engine)
    session = Session()

    # 需求：主机c1对应的所有用户------方法三（1）
    host_obj = session.query(Host).filter(Host.hostname == 'c1').first()  # 获取主机名为c1的主机数据
    print(host_obj.host_user)   # 获取数据对应的host_user的所有数据

```

# sqlalchemy多对多关系-3

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""
http://www.cnblogs.com/wupeiqi/articles/5699254.html

创建表：
    普通表
    额外创建一张关系表，表示表之间的关系，指定约束
操作表：
    类：指定查询结果的输出形式（__repr__方法）
    单表操作
    连表操作：
            relationship正向、反向查询
            session.query(User).join(Group, isouter=True).all()   默认为innerjoin操作，加了isouter=True就是left join操作
"""
from sqlalchemy import create_engine, Column, Integer, String, ForeignKey, Table
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker,relationship

# 创建数据库连接
engine = create_engine("mysql+pymysql://root:123456@192.168.1.22:3306/sqlalchemytest", max_overflow=5)
# 创建基类
Base = declarative_base()

# 使用relationship建立与其他表的关系时，若不是在有外键的表中进行创建，多对多表不能使用class类进行创建，而是使用sqlalchemy.Table创建
# HostToHostUser = Table('host_to_hostuser', Base.metadata,
#                        Column('host_id', ForeignKey("host.id"), primary_key=True),
#                        Column('hostuser_id', ForeignKey("hostuser.id"), primary_key=True),
#                        )

# 也可不用Table方法来创建关系表，class创建（要放在要管理的表的前面）
class HostToHostUser(Base):
    __tablename__ = 'host_to_hostuser'
    id = Column(Integer, primary_key=True, autoincrement=True)
    host_id = Column(Integer, ForeignKey('host.id'))
    hostuser_id = Column(Integer, ForeignKey('hostuser.id'))

class Host(Base):
    __tablename__ = 'host'
    id = Column(Integer, primary_key=True, autoincrement=True)
    hostname = Column(String(32))
    port = Column(String(32))
    ip = Column(String(32))

    # host_user = relationship('HostUser', secondary=HostToHostUser, backref='u')
    # 使用class创建关系表，关联的时候要使用HostToHostUser.__table__
    host_user = relationship('HostUser', secondary=HostToHostUser.__table__, backref='u')

class HostUser(Base):
    __tablename__ = 'hostuser'
    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String(32))


# class HostToHostUser(Base):
#     __tablename__ = 'host_to_hostuser'
#     id = Column(Integer, primary_key=True, autoincrement=True)
#     host_id = Column(Integer, ForeignKey('host.id'))
#     hostuser_id = Column(Integer, ForeignKey('hostuser.id'))

    # host = relationship("Host", backref='h')
    # host_user = relationship("HostUser", backref='u')


# 创建数据库
def init_db():
    Base.metadata.create_all(engine)


# 删除数据库
def drop_db():
    Base.metadata.drop_all(engine)

if __name__ == '__main__':
    # init_db()

    # 创建session，用来操作数据库
    Session = sessionmaker(bind=engine)
    session = Session()

    # 需求：主机c1对应的所有用户------方法三（2）
    host_obj = session.query(Host).filter(Host.hostname == 'c1').first()  # 获取主机名为c1的主机数据
    print(host_obj.host_user)   # 获取数据对应的host_user的所有数据

```


