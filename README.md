import { useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { Input } from "@/components/ui/input"; import { Textarea } from "@/components/ui/textarea"; import { format } from "date-fns";

export default function ErsatzteileinkaufLandena() { const [orders, setOrders] = useState([]); const [form, setForm] = useState({ teil: "", lieferant: "", bestelldatum: "", lieferdatum: "", anmerkungen: "", });

const handleChange = (e) => { setForm({ ...form, [e.target.name]: e.target.value }); };

const handleSubmit = () => { if (!form.teil || !form.lieferant || !form.bestelldatum || !form.lieferdatum) return; setOrders([...orders, { ...form, status: "Offen" }]); setForm({ teil: "", lieferant: "", bestelldatum: "", lieferdatum: "", anmerkungen: "" }); };

const getStatus = (lieferdatum) => { const today = new Date(); const delivery = new Date(lieferdatum); const diff = (delivery - today) / (1000 * 60 * 60 * 24); if (diff < 0) return "🔴 Überfällig"; if (diff <= 2) return "🟡 Fällig bald"; return "🟢 Offen"; };

return ( <div className="p-6 max-w-4xl mx-auto space-y-6"> <h1 className="text-3xl font-bold">Ersatzteileinkauf Landena</h1>

<Card>
    <CardContent className="space-y-4 pt-6">
      <div className="grid grid-cols-2 gap-4">
        <Input name="teil" placeholder="Teil" value={form.teil} onChange={handleChange} />
        <Input name="lieferant" placeholder="Lieferant" value={form.lieferant} onChange={handleChange} />
        <Input type="date" name="bestelldatum" value={form.bestelldatum} onChange={handleChange} />
        <Input type="date" name="lieferdatum" value={form.lieferdatum} onChange={handleChange} />
      </div>
      <Textarea name="anmerkungen" placeholder="Anmerkungen" value={form.anmerkungen} onChange={handleChange} />
      <Button onClick={handleSubmit}>Bestellung hinzufügen</Button>
    </CardContent>
  </Card>

  <div className="space-y-4">
    {orders.map((order, index) => (
      <Card key={index} className="border-l-4" style={{ borderColor: getStatus(order.lieferdatum).includes("Überfällig") ? "#dc2626" : getStatus(order.lieferdatum).includes("bald") ? "#facc15" : "#16a34a" }}>
        <CardContent className="pt-4">
          <div className="flex justify-between items-center">
            <div className="font-semibold text-lg">{order.teil} – {order.lieferant}</div>
            <div className="text-sm">{getStatus(order.lieferdatum)}</div>
          </div>
          <div className="text-sm mt-1">📅 Bestellt am: {format(new Date(order.bestelldatum), 'dd.MM.yyyy')} | 🚚 Lieferung: {format(new Date(order.lieferdatum), 'dd.MM.yyyy')}</div>
          {order.anmerkungen && <div className="text-sm mt-2">📝 {order.anmerkungen}</div>}
        </CardContent>
      </Card>
    ))}
  </div>
</div>

); }
