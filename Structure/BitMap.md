```
class BitMap{
public:
    char *bitmap;
    int size;
public:
    BitMap(){
        bitmap = NULL;
        size = 0;
    }
    BitMap(int size){
        bitmap = (char*)malloc(size/8+(size%8)!=0);
        if (bitmap == NULL) {
            printf("ErroR In BitMap Constractor!\n");
        }else{
            memset(bitmap, 0x0, (size/8+(size%8)!=0) * sizeof(char));
            this->size = size;
        }
    }
    
    int bitmapSet(int index,bool flag){
        if (index >= size) {
            return 0;
        }else{
            bitmap[index/8] &= ~(unsigned char)(0x1 << index%8);
            bitmap[index/8] |= (unsigned char)(flag << index%8);
            return 1;
        }
    }
    
    int bitmapGet(int index){
        if (index >= size) {
            return 0;
        }else{
            return (bitmap[index/8] & (unsigned char)(0x1<<(index%8))) > 0 ? 1 : 0;
        }
    }
};
```

