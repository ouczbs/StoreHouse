

# Lua 栈

```c++
TValue stack[max_stack_len];// 创建Lua栈

struct TValue{
    Value value;
    int tt;//索引
};
union Value{
    GCObject * gc; // table、 func、closure、 string、userdata、thread
    void * p; //light userdata
    lua Number n; // int float
    bool b;
};
lua_pushcclosure(L, func, 0); // 创建并压入一个闭包
lua_createtable(L, 0, 0);        // 新建并压入一个表
lua_pushnumber(L, 343);      // 压入一个数字
lua_pushstring(L, “mystr”);   // 压入一个字符串
```

## 垃圾回收

- `number, boolean, nil, light userdata` 四种类型的值是直接存在栈上元素里的, 和垃圾回收无关.
- `string, table, closure, userdata, thread` 存在栈上元素里的只是指针, 他们都会在生命周期结束后被垃圾回收.

## 栈操作

     "如果你想要什么, 你告诉我, 我来产生"就可以保证, 凡是lua中的变量, lua要负责这些变量的生命周期和垃圾回收, 所以, 必须由lua来创建这些值(在创建时就加入了生命周期管理要用到的簿记信息)
    
     "然后放到栈上, 你只能通过api来操作这个值", lua api给c提供了一套完备的操作界面, 这个就相当于约定的通信协议, 如果lua客户使用这个操作界面, 那么lua本身不会出现任何"意料之外"的错误.
    
     "我只管我的世界"这句话体现了lua和c/c++作为两个不同系统的分界, c/c++中的值, lua是不知道的, lua只负责它的世界

### Push

```c++
void lua_pushnumber (lua_State *L, lua_Number n) ;
void lua_pushboolean (lua_State *L, int b ;
void lua_pushnil (lua_State *L);
                      
Stack[L->top-1] = value;
L->top++;    
```

### ToValue

0-nil，1-bool，2-lightuserdata，3-number，4-string，5-table，6-function，7-userdata，8-thread。-1代表啥也没有（不是nil）

```c++
lua_tostring(L, i);
lua_toboolean(L, i);
lua_tonumber(L, i);
lua_typename(L, t);
case Stack[i].tt
return union.xx
```

### Pop

```c++
lua_gettable()
```





```c++
int   lua_gettop (lua_State *L);            //返回栈顶索引（即栈长度）  
void  lua_settop (lua_State *L, int idx);   //                
void  lua_pushvalue (lua_State *L, int idx);//将idx索引上的值的副本压入栈顶  
void  lua_remove (lua_State *L, int idx);   //移除idx索引上的值  
void  lua_insert (lua_State *L, int idx);   //弹出栈顶元素，并插入索引idx位置  
void  lua_replace (lua_State *L, int idx);  //弹出栈顶元素，并替换索引idx位置的值
```



# Lua 对象

## 数值、布尔

### 流程

- 省略创建对象以及GC操作
- 无返回值

### 源码

```c++
void lua_pushnumber (lua_State *L, lua_Number n) ;
void lua_pushboolean (lua_State *L, int b ;
void lua_pushnil (lua_State *L);
```



## 字符串

### 流程

- 创建TString 对象
  - 系统字符串缓存
  - 短字符串缓存
  - 长字符串创建
- 栈顶TValue指向TString对象
  - 栈顶向上增长
  - 检查LuaGC
- 返回新字符串地址
  - Lua地址

### 源码

```c++
char *lua_pushstring (lua_State *L, const char *s) {
    //if(s == NULL)setnilvalue(s2v(L->top));
    TString *ts = luaS_new(L, s);
    setsvalue2s(L, L->top, ts);
    s = getstr(ts);  /* internal copy's address */
    api_incr_top(L);
  	luaC_checkGC(L);
    return s
}
TString *luaS_new (lua_State *L, const char *str) {
  TString **p = hashfind(G(L)->strcache[i]);//系统字符串缓存
  if(p) return *p;
  p[0] = luaS_newlstr(L, str, strlen(str));
  return p[0]
}
TString *luaS_newlstr (lua_State *L, const char *str) {
  TString **p = hashfind(G(L)->strcache[i]);//系统字符串缓存
  if(p) return *p;
  p[0] = luaS_newlstr(L, str, strlen(str));
  return p[0]
}
TString *luaS_newlstr (lua_State *L, const char *str, size_t l) {
  if (l <= LUAI_MAXSHORTLEN)  /*length less 40 is short string? */
    return internshrstr(L, str, l);
  else {
    TString *ts = luaS_createlngstrobj(L, l);
    memcpy(getstr(ts), str, l * sizeof(char));
    return ts;
  }
}
static TString *internshrstr (lua_State *L, const char *str, size_t l) {
  TString **p = hashfind(G(L)->strt);
  if(p) return *p;
  TString * ts = createstrobj(L, l, LUA_VSHRSTR, h);
  memcpy(getstr(ts), str, l * sizeof(char));
  hashinsert(p,ts);
  return ts;
}
```

## 表

### 流程



### 源码

## 闭包

## 全局

### set global

set 之前应该先往栈压入数据，再给数据压入命名，最后弹出数据和命名

```c++
LUA_API void lua_setglobal (lua_State *L, const char *name) {
  const TValue *G = getGtable(L);
  auxsetstr(L, getGtable(L), name);
}
static void auxsetstr (lua_State *L, const TValue *t, const char *k) {
  const TValue *slot;
  TString *str = luaS_new(L, k);
  api_checknelems(L, 1);
  if (luaV_fastget(L, t, str, slot, luaH_getstr)) {
    luaV_finishfastset(L, t, slot, s2v(L->top - 1));
    L->top--;  /* pop value */
  }
  else {
    setsvalue2s(L, L->top, str);  /* push 'str' (to make it a TValue) */
    api_incr_top(L);
    luaV_finishset(L, t, s2v(L->top - 1), s2v(L->top - 2), slot);
    L->top -= 2;  /* pop value and key */
  }
  lua_unlock(L);  /* lock done by caller */
}
```

## set table

```c++
void luaV_finishset (lua_State *L, const TValue *t, TValue *key,
                     TValue *val, const TValue *slot) {
  int loop;  /* counter to avoid infinite loops */
  for (loop = 0; loop < MAXTAGLOOP; loop++) {
    const TValue *tm;  /* '__newindex' metamethod */
    if (slot != NULL) {  /* is 't' a table? */
      Table *h = hvalue(t);  /* save 't' table */
      lua_assert(isempty(slot));  /* slot must be empty */
      tm = fasttm(L, h->metatable, TM_NEWINDEX);  /* get metamethod */
      if (tm == NULL) {  /* no metamethod? */
        luaH_finishset(L, h, key, slot, val);  /* set new value */
        invalidateTMcache(h);
        luaC_barrierback(L, obj2gco(h), val);
        return;
      }
      /* else will try the metamethod */
    }
    else {  /* not a table; check metamethod */
      tm = luaT_gettmbyobj(L, t, TM_NEWINDEX);
      if (l_unlikely(notm(tm)))
        luaG_typeerror(L, t, "index");
    }
    /* try the metamethod */
    if (ttisfunction(tm)) {
      luaT_callTM(L, tm, t, key, val);
      return;
    }
    t = tm;  /* else repeat assignment over 'tm' */
    if (luaV_fastget(L, t, key, slot, luaH_get)) {
      luaV_finishfastset(L, t, slot, val);
      return;  /* done */
    }
    /* else 'return luaV_finishset(L, t, key, val, slot)' (loop) */
  }
  luaG_runerror(L, "'__newindex' chain too long; possible loop");
}
```

