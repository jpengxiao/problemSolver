边着色问题

边着色问题\(最优颜色数\)是NP问题，需要放松一个或多个标准：

仅工作在图的子集，特殊的图；使用超出最优的颜色数；并不是所有时候都是多项式时间





1.简单图

Maris&Gries－扇和路径

```
vector<int> maximal_fan(vector<vector<int>>& matrix,map<int,set<int>>& free_color,int n,int u,int v){
    //着色过的所有邻居
    vector<int> neighbor;
    for(int i=0;i<n;i++){
        if(matrix[u][i]!=-1)
            neighbor.push_back(i);
    }
    //F[0:k-1]是u的一个最大扇，且F[0]=v
    vector<int> maxFan;
    maxFan.push_back(v);
    //当前节点
    bool flag;
    int cur=v;
    do{
        flag=false;
        for(int i=0;i<(int)neighbor.size();i++){
            //若邻居颜色为cur的free_color
            if(free_color[cur].count(matrix[u][neighbor[i]])>0){
                cur=neighbor[i];
                maxFan.push_back(cur);
                neighbor.erase(neighbor.begin()+i);
                flag=true;
                break;
            }
        }
    }while(flag);
    return maxFan;
}
void invert_cd_path(vector<vector<int>>& matrix,map<int,set<int>>& free_color,int n,int u,int c,int d){
    int cur=u;
    set<int> set;
    bool flag;
    do{
        flag=false;
        //扫描当前位置的边
        for(int i=0;i<n;i++){
            if(i==cur||set.count(i)>0) //防止回路
                continue;
            if(matrix[cur][i]==c||matrix[cur][i]==d){
                //起点和终点的free_color才会变化
                if(matrix[cur][i]==c){
                    matrix[cur][i]=d;
                    matrix[i][cur]=d;
                }else{
                    matrix[cur][i]=c;
                    matrix[i][cur]=c;
                }
                set.insert(cur);
                cur=i;
                flag=true;
                break;
            }
        }
    }while(flag);
    if(set.size()>0){
        free_color[u].insert(d);  //首节点释放了d颜色
        free_color[u].erase(c);   //首节点使用了c颜色
        free_color[cur].insert(set.size()%2==0?c:d); //尾节点释放c(偶数)或d(奇数)颜色
        free_color[cur].erase(set.size()%2==0?d:c);  //尾节点使用d(偶数)或c(奇数)颜色
    }
}
void rotateFan(vector<vector<int>>& matrix,map<int,set<int>>& free_color,vector<int>& newFan,int u,int d){
    if(newFan.size()>0){
        for(int i=0;i<newFan.size()-1;i++){
            if(matrix[u][newFan[i]]!=-1)
                free_color[newFan[i]].insert(matrix[u][newFan[i]]); //newFan[i]加上原有的颜色
            //颜色左移
            matrix[u][newFan[i]]=matrix[u][newFan[i+1]];
            matrix[newFan[i]][u]=matrix[u][newFan[i+1]];
            if(matrix[u][newFan[i+1]]!=-1)
                free_color[newFan[i]].erase(matrix[u][newFan[i+1]]); //newFan[i]减去当前的颜色
        }
        if(matrix[u][newFan[newFan.size()-1]]!=-1)  //最后一个加上原有的颜色
            free_color[newFan[newFan.size()-1]].insert(matrix[u][newFan[newFan.size()-1]]);
        matrix[u][newFan[newFan.size()-1]]=d;
        matrix[newFan[newFan.size()-1]][u]=d;
        free_color[u].erase(d);
        free_color[newFan[newFan.size()-1]].erase(d);
    }
}
bool msira_gries(vector<vector<int>>& matrix,vector<vector<int>>& f,map<int,set<int>>& free_color,pair<int,int>& edge,int n,int m){
    //(u,v)是任意一条边
    int u=edge.first,v=edge.second;
    //F[0:k-1]是u的一个最大扇，且F[0]=v
    vector<int> maxFan=maximal_fan(matrix, free_color, n, u, v);    
    //令c是对于u未被使用的一种颜色，d是对于F[k-1]未被使用的一种颜色（存在空的情况）
    int c=(*free_color[u].begin()),d=(*free_color[maxFan[maxFan.size()-1]].begin());
    if(free_color[maxFan[maxFan.size()-1]].size()==0)
        return false;  
    //翻转cdu路径，路径最多构成环(将u的颜色d空闲出来)
    invert_cd_path(matrix,free_color,n,u,c,d);
    //令 w ∈ V(G) 使得 w ∈ F, F'=[F[0]...w] 是一个扇，且颜色d对于w未被使用
    vector<int> newFan;
    for(int i=0;i<maxFan.size();i++){
        newFan.push_back(maxFan[i]);
        if(free_color[maxFan[i]].count(d)!=0)
            break;
    }
    //旋转扇F'并设置c(u,w)=d
    rotateFan(matrix,free_color,newFan,u,d);
    return true;
}
```

2.多重图

贪心－首次匹配

```
vector<int> Greedy(vector<vector<int>>& matrix,vector<pair<int,int>>& edges,int n){
    int cols=0;
    vector<int> edges_col;
    vector<set<int>> used_color(n);
    for(pair<int,int> pair:edges){
        int c=0;
        while(used_color[pair.first].count(c)>0||used_color[pair.second].count(c)>0){ c++; }
        edges_col.push_back(c);
        used_color[pair.first].insert(c);
        used_color[pair.second].insert(c);
        cols=c>cols?c:cols;
    }
    return edges_col;
}
```

线性规划－最大匹配

```cpp
//对所有边进行着色，颜色数不确定
vector<int> edgecoloring_lp(vector<pair<int,int>> edges,int n){
    int m=(int)edges.size(); //edges
    int c=0; //colors
    vector<vector<int>> inc_mat(n,vector<int>(m)); //incidence_matrix (顶点,边)
    vector<int> right_vec(n,0); //rignt_vector (右端向量)
    vector<int> w(m,-1); //weight (权重)
    for(int i=0;i<m;i++){
        inc_mat[edges[i].first][i]=1;
        inc_mat[edges[i].second][i]=1;
        right_vec[edges[i].first]=1;
        right_vec[edges[i].second]=1;
    }
    //min(-(e1+e2+...+em))
    vector<int> edges_col(m,-1);
    while(m>0){
        vector<int> res=bintprop(w,inc_mat,right_vec); //0-1线性规划
        for(int i=0;i<m;i++){
            if(res[i]==1){
                edges_col[i]=c;
                inc_mat[edges[i].first][i]=0;
                inc_mat[edges[i].second][i]=0;
                m--;
            }
        }
        c++;
    }
    return edges_col;
}
```

