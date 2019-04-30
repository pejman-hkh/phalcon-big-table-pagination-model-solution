# phalcon-big-table-pagination-model-solution
Phalcon big table pagination model solution


Phalcon in big table pagination has problem, it load all result in php array and break it in array. This cause much memory usage in php and slow performance in site load.

Instead fetch all rows in php we should use limit and offset to get rows that we need.

I write my own pagination model that solved that problem.

```php
class MyPaginatorModel  {

    function __construct( $modelName, $data ) {

        $this->modelName = $modelName;
        $this->data = $data;
    }

    public function getPaginate() {
        $data = $this->data;
        $modelName = $this->modelName;

        $count = $modelName::count([
            $data['sql'],
            'bind' => $data['bind'],
        ]);

        $number = $limit = $data['limit'];
        $page = (int)$data['page'];

        $o = new \stdClass();
        $o->items = $items = $modelName::find([
            $data['sql'],
            'bind' => $data['bind'],
            'order' => $data['order'],
            'limit' => $limit,
            'offset' =>  intval( $page * $limit - $limit ),
       ]);

        $o->current = intval($page);
        $o->total_pages = $total_pages = ceil($count / $number);
        $o->total_items = $count;

        $o->next = $page >= $total_pages ? $page : $page + 1;
        $o->before = $page <= 1 ? 1 : $page - 1;
        $o->last = $total_pages;

        return $o;
    }
}

```

# Usage

```php
$paginator = new MyPaginatorModel('Files',
    [
        'sql' => $qry,
        'bind' => $bind,
        'order' => 'ID DESC',
        'limit' => $items,
        'page' => $page,
    ]
);
```