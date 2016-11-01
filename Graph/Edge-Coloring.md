边着色问题

1.简单图

2.多重图



解法：

线性规划－最大匹配

输入：所有边    输出：边对应的颜色

`vector<int> edgecoloring_lp(vector<pair<int,int>> edges,int n){`

` int m=(int)edges.size();`

` int c=0; //colors`



` vector<vector<int>> inc_mat(n,vector<int>(m)); //incidence_matrix (顶点,边)`

` vector<int> right_vec(n,0); //rignt_vector (右端向量)`

` vector<int> w(m,-1); //weight (权重)`

` for(int i=0;i<m;i++){`

` inc_mat[edges[i].first][i]=1;`

` inc_mat[edges[i].second][i]=1;`

` right_vec[edges[i].first]=1;`

` right_vec[edges[i].second]=1;`

` }`



` vector<int> edges_col(m,-1);`

` //0-1线性规划 min(-(e1+e2+...+em))`

` while(m>0){`

` vector<int> res=bintprop(w,inc_mat,right_vec); //0-1线性规划`

` for(int i=0;i<m;i++){`

` if(res[i]==1){`

` edges_col[i]=c;`

` inc_mat[edges[i].first][i]=0;`

` inc_mat[edges[i].second][i]=0;`

` m--;`

` }`

` }`

` c++;`

` }`

` return edges_col;`

`}`









