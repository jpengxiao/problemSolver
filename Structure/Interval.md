```java
class Interval {
    int start;
    int end;
    Interval() { start = 0; end = 0; }
    Interval(int s, int e) { start = s; end = e; }
    public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
    	if(newInterval==null) return intervals;
    	List<Interval> res=new LinkedList<Interval>();
    	int idx=0;
    	while(idx<intervals.size()&&intervals.get(idx).end<newInterval.start) res.add(intervals.get(idx++));
    	if(idx==intervals.size()){ res.add(newInterval); return res; }
    	while (idx < intervals.size() && intervals.get(idx).start <= newInterval.end) {
            newInterval.start=Math.min(intervals.get(idx).start, newInterval.start);
            newInterval.end=Math.max(intervals.get(idx).end, newInterval.end);       
            idx++;
        }
    	res.add(newInterval);
    	while(idx<intervals.size()){ res.add(intervals.get(idx++)); } 
        return res;
    }
    public List<Interval> merge(List<Interval> intervals) {
		if(intervals==null||intervals.size()<=1) return intervals;
        List<Interval> res=new LinkedList<Interval>();
        Collections.sort(intervals, new Comparator<Interval>(){
			@Override
			public int compare(Interval o1, Interval o2) {
				return Integer.compare(o1.start,o2.start);
			}
        });
        //区间合并
        Interval tmp=new Interval(intervals.get(0).start,intervals.get(0).end);
        for(int i=1;i<intervals.size();i++){
        	if(tmp.end>=intervals.get(i).start){
        		tmp.end=Math.max(tmp.end, intervals.get(i).end);
        	}else{
        		res.add(tmp);
        		tmp=new Interval(intervals.get(i).start,intervals.get(i).end);
        	}
        	if(i==intervals.size()-1){
        		res.add(tmp);
        	}
        }
        return res;
    }
}
```

